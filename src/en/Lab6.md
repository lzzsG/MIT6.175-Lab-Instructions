# Lab 6: RISC-V Processor with 6-Stage Pipeline and Branch Prediction

> **Lab 6 due date:** Monday, November 7, 2016, at 11:59:59 PM EDT.
>
> Your deliverables for Lab 6 are:
>
> - your answers to Exercises 1 through 4 in `SixStage.bsv`, `Bht.bsv`, and `SixStageBHT.bsv`
> - your answers to Discussion Questions 1 through 9 in `discussion.txt`

## Introduction

This lab is your introduction to realistic RISC-V pipelines and branch prediction. At the end of this lab, you will have a six-stage RISC-V pipeline with multiple address and branch predictors working together.

> **Note:** In this lab, we use one-bit global epochs (instead of unbounded distributed epochs) to kill wrong path instructions. Please study the Global Epoch slides: [[pptx\]](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab6/global-epoch.pptx) [[pdf\]](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab6/global-epoch.pdf) to understand the global epoch scheme. The content of the slides will also be covered in tutorial.

## Additions to the lab infrastructure

### New included files

The following files appear in `src/includes/`:

| Filename         | Description                                                  |
| :--------------- | :----------------------------------------------------------- |
| `FPGAMemory.bsv` | A wrapper for block RAM commonly found on FPGAs. This has an identical interface as the DelayedMemory from the previous lab. |
| `SFifo.bsv`      | Three searchable FIFO implementations: one based off of a pipeline FIFO, one based off of a bypass FIFO, and the other based off of a conflict-free FIFO. All implementations assume search is done immediately before enq. |
| `Scoreboard.bsv` | Three scoreboard implementations based off of searchable FIFOs. The pipeline scoreboard uses a pipeline searchable FIFO, the bypass scoreboard uses a bypass searchable FIFO, and the conflict-free scoreboard uses a conflict-free searchable FIFO. |
| `Bht.bsv`        | An empty file in which you will implement a branch history table (BHT). |

### New assembly tests

The following file appears in `programs/assembly/src`:

| Filename           | Description                                                  |
| :----------------- | :----------------------------------------------------------- |
| `bpred_j_noloop.S` | An assembly test similar to `bpred_j.S`, but the outer loop is removed. |

### New source files

The following files appear in `src/`:

| Filename            | Description                                                  |
| :------------------ | :----------------------------------------------------------- |
| `TwoStage.bsv`      | An initial two-stage pipelined RISC-V processor that uses a BTB for address prediction. Compile with `twostage` target. |
| `SixStage.bsv`      | An empty file in which you will extend the two-stage pipeline into a six-stage pipeline. Compile with `sixstage` target. |
| `SixStageBHT.bsv`   | An empty file in which you will integrate a BHT into the six-stage pipeline. Compile with `sixstagebht` target. |
| `SixStageBonus.bsv` | An empty file in which you can improve the previous processor for bonus credit. Compile with `sixstagebonus` target. |

### Testing improvements

In the previous lab, the command `build -v <proc_name>` (run from the `scemi/sim/` directory) was used to build `bsim_dut` and `tb`. In this lab, this command builds `<proc_name>_dut` instead of `bsim_dut` so switching between processor types does not delete other processor builds.

Simulation scripts now require you to specify the target processor:

```
$ ./run_asm.sh <proc_name>
$ ./run_bmarks.sh <proc_name>
```

Simulating a single test requires you to run the correct simulation executable:

```
$ cp ../../programs/build/{assembly,benchmarks}/vmh/<test_name>.riscv.vmh mem.vmh
$ ./<proc_name>_dut > out.txt &
$ ./tb
```

## Two-stage pipeline: `TwoStage.bsv`

`TwoStage.bsv` contains a two-stage pipelined RISC-V processor. This processor differs from the processor you built in the previous lab because it reads register values in the first stage and there is data hazard.

> **Discussion Question 1 (10 Points):** Debugging practice!
>
> If you replace the BTB with a simple `pc + 4` address prediction, the processor still works, but it does not perform as well. If you replace it with a really bad predictor that predicts `pc` is the next instruction for each `pc`, it should still work but have even worse performance because each instruction would require redirection (unless the instruction loops back to itself). If you actually set the prediction to `pc`, you will get errors in the assembly tests; the first one will be from `cache.riscv.vmh`.
>
> - What is the error you get?
> - What is happening in the processor to cause that to happen?
> - Why do not you get this error with PC+4 and BTB predictors?
> - How would you fix it?
>
> You do not actually have to fix this bug, just answer the questions. (Hint: look at the `addr` field of `ExecInst` structure.)



## Six-stage pipeline: `SixStage.bsv`

The six-stage pipeline should be divided into the following stages:

- Instruction Fetch -- request instruction from iMem and update PC
- Decode -- receive response from iMem and decode instruction
- Register Fetch -- read from the register file
- Execute -- execute the instruction and redirect the processor if necessary
- Memory -- send memory request to dMem
- Write Back -- receive memory response from dMem (if applicable) and write to register file

`IMemory` and `DMemory` instances should be replaced with instances of `FPGAMemory` to enable later implementation on FPGA.

> **Exercise 1 (20 Points):** Starting with the two-stage implementation in `TwoStage.bsv`, replace each memory with `FPGAMemory` and extend the pipeline into a six-stage pipeline in `SixStage.bsv`. In simulation, benchmark `qsort` may take longer time (21 seconds on TA's desktop, and it may take even longer on the vlsifarm machines).

Notice that the two-stage implementation uses a conflict-free register file and scoreboard. However, you could use the pipelined or bypassed versions of these components for better performance. Also, you may want to change the size of scoreboard.

> **Discussion Question 2 (5 Points):** What evidence do you have that all pipeline stages can fire in the same cycle?

> **Discussion Question 3 (5 Points):** In your six-stage pipelined processor, how many cycles does it take to correct a mispredicted instruction?

> **Discussion Question 4 (5 Points):** If an instruction depends on the result of the instruction immediately before it in the pipeline, how many cycles is that instruction stalled?

> **Discussion Question 5 (5 Points):** What IPC do you get for each benchmark?

## Adding a branch history table: `SixStageBHT.bsv`

The branch history table (BHT) is a structure that keeps track of the history of branches and is used in direction prediction. Your BHT should be indexed by a parameterized number of bits taken from the program counter -- typically bit n+1 down to bit 2 since bits 1 and 0 will always be zero. Each index should have a two-bit saturating counter. Do not include any valid bits or tags in the BHT; we are not concerned about aliasing in our predictions.

> **Exercise 2 (20 Points):** Implement a branch history table in `Bht.bsv` that uses a parameterizable number of bits as an index into the table.

> **Discussion Question 6 (10 Points):** Planning!
>
> One of the hardest things about this lab is properly training and integrating the BHT into the pipeline. There are many mistakes that can be made while still seeing decent results. By having a good plan based on the fundamentals of direction prediction, you will avoid many of those mistakes.
>
> For this discussion question, state your plan for integrating the BHT into the pipeline. The following questions should help guide you:
>
> - Where will the BHT be positioned in the pipeline?
> - What pipeline stage performs lookups into the BHT?
> - In which pipeline stage will the BHT prediction be used?
> - Will the BHT prediction need to be passed between pipeline stages?
>
> - How to redirect PC using BHT prediction?
> - Do you need to add a new epoch?
> - How to handle the redirect messages?
> - Do you need to change anything to the current instruction and its data structures if redirecting?
>
> - How will you train the BHT?
> - Which stage produces training data for the BHT?
> - Which stage will use the interface method to train the BHT?
> - How to send training data?
> - For which instructions will you train the BHT?
>
> - How will you know if your BHT works?

> **Exercise 3 (20 Points):** Integrate a 256-entry (8-bit index) BHT into the six-stage pipeline from `SixStage.bsv`, and put the results in `SixStageBHT.bsv`.

> **Discussion Question 7 (5 Points):** How much improvement do you see in the `bpred_bht.riscv.vmh` test over the processor in `SixStage.bsv`?

> **Exercise 4 (10 Points):** Move address calculation for JAL up to the decode stage and use the redirect logic used by the BHT to redirect for these instructions too.

> **Discussion Question 8 (5 Points):** How much improvement do you see in the `bpred_j.riscv.vmh` and `bpred_j_noloop.riscv.vmh` tests over the processor in `SixStage.bsv`?

> **Discussion Question 9 (5 Points):** What IPC do you get for each benchmark? How much improvement is this over the original six-stage pipeline?

> **Discussion Question 10 (Optional):** How long did it take you to complete this lab?

Remember to push your code with `git push` when you're done.

## Bonus improvements: `SixStageBonus.bsv`

This section looks at two ways to speed up indirect jumps to addresses stored in registers (JALR).

> **Exercise 5 (10 Bonus Points):** JALR instructions have known target addresses in the register fetch stage. Add a redirection path for JALR instructions in the register fetch stage and put the results in `SixStageBonus.bsv`. The `bpred_ras.riscv.vmh` test should give slightly better results with this improvement.

Most JALR instructions found in programs are used as returns from function calls. This means the target address for such a return was written into the return address register `x1` (also called `ra`) by a previous JAL or JALR instruction that initiates the function call.

To make better prediction of JALR instructions, we can introduce the return address stack (RAS) to our processor. According to RISC-V ISA, a JALR instruction with `rd=x0` and `rs1=x1` is commonly used as the return instruction from a function call. Besides, a JAL or JALR instruction with `rd=x1` is commonly used as the jump to initiate a function call. Therefore, we should push the RAS for JAL/JALR instruction with `rd=x1`, and pop the RAS for JALR instruction with `rd=x0` and `rs1=x1`.

> **Exercise 6 (10 Bonus Points):** Implement a return address stack and integrate it into the Decode stage of your processor (`SixStageBonus.bsv`). An 8 element stack should be enough. If the stack fills up, you could simply discard the oldest data. The `bpred_ras.riscv.vmh` test should give an even better result with this improvement. If you implemented the RAS in a separate BSV file, make sure to add it to the git repository for grading.

------

© 2016 [Massachusetts Institute of Technology](http://web.mit.edu/). All rights reserved.
