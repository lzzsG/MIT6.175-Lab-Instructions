# Lab 8: RISC-V Processor with Exceptions

> **Lab 8 due date:** Friday, November 25, at 11:59:59 PM EST.
>
> Your (exceptionally few) deliverables for Lab 8 are:
>
> - Your answer to Exercise 1 in `ExcepProc.bsv`
> - Your answer to Discussion Question 1 in `discussion.txt`

## Introduction

In this lab you will add exceptions to a one-cycle RISC-V processor. With the support of exception, we will be able to do the following two things:

1. Implement `printInt()`, `printChar()`, and `printStr()` functions as system calls.
2. Emulate the unsupported multiply instruction (`mul`) in a software exception handler.

We are using a one-cycle processor so you can focus on how exceptions work without including the complexities due to pipelining.

You have been given all the required programs for testing your processor. You only need to add hardware support to run exceptions. The following sections cover what has been changed in the processor and what you need to do.

## CSRs

The `mkCsrFile` module in `src/includes/CsrFile.bsv` has been extended with new CSRs required for implementing exceptions.

Below is the summary of new CSRs in the `mkCsrFile` module. Your software can manipulate these CSRs using the `csrr`, `csrw`, and `csrrw` instructions.

| Control Register Name | Description                                                  |
| :-------------------- | :----------------------------------------------------------- |
| `mstatus`             | The low 12 bits of this register store a 4-element stack of privilege/user mode (PRV) and interrupt enable (IE) bits. Each stack element is 3 bits wide. For example, `mstatus[2:0]` corresponds to the top of the stack, and contains the current PRV and IE bits. Specifically, `mstatus[0]` is the IE bit, and interrupts are enabled if IE = 1. `mstatus[2:1]` contains the PRV bits. If the processor is in user mode, it should be set to `2'b00`. If the processor is in machine (privileged) mode, it should be set to `2'b11`. Other stack elements (i.e. `mstatus[5:3], ..., mstatus[11:9]`) have the same construction. When an exception is taken, the stack will be "pushed" by left-shifting 3 bits. As a result, the new PRV and IE bits (e.g. machine mode and interrupt disabled) are now stored into `mstatus[2:0]`. Conversely, when we return from an exception using the `eret` instruction, the stack is "popped" by right-shifting 3 bits. Bits `mstatus[2:0]` will contain their original values, and `mstatus[11:9]` is assigned to (user mode, interrupt enabled). |
| `mcause`              | When an exception occurs, the cause is stored in `mcause`. `ProcTypes.bsv` contains two cause values for the exceptions that we will implement in this lab:`excepUnsupport`: an unsupported instruction exception.`excepUserECall`: a system call. |
| `mepc`                | When an exception occurs, the PC of the instruction causing the exception is stored in `mepc`. |
| `mscratch`            | It stores the pointer to a "safe" data section that can be used to store all general purpose register (GPR) values when exception happens. This register is completely manipulated by software in this lab. |
| `mtvec`               | The **t**rap **vec**tor is a read-only register, and it stores the start address of the exception handler program. The processor should set PC to `mtvec` when an exception happens. |

The `mkCsrFile` module also incorporates additional interface methods, which should be self-explanatory.

## Decode Logic

The decoding logic has also been extended to support exceptions. The functionality of the following three new instructions is summarized below:

| Instruction          | Description                                                  |
| :------------------- | :----------------------------------------------------------- |
| `eret`               | This instruction is used to return from exception handling. It is decoded to a new `iType` of `ERet` and everything else invalid and not taken. |
| `ecall` (or `scall`) | This instruction is the system call instruction. It is decoded to a new `iType` of `ECall` and everything else invalid and not taken. |
| `csrrw rd, csr, rs1` | This instruction writes the value of `csr` into `rd`, and writes the value of `rs1` into `csr`. That is, it performs `rd <- csr; csr <- rs1`. Both `rd` and `rs1` are GPRs, while `csr` is a CSR. This instruction replaces the `csrw` instruction we have used before, because `csrw` is just a special case of `csrrw`. This instruction is decoded to a new `iType` of `Csrrw`. Since `csrrw` will write two registers, the `ExecInst` type in `ProcTypes.bsv` incorporates a new field "`Data csrData`", which contains the data to be written into `csr`. |

The `eret` and `csrrw` instructions are allowed in machine (privileged) mode. To detect the illegal use of such instructions in user mode, the `decode` function in `Decode.bsv` takes a second argument "`Bool inUserMode`". This argument should be set to `True` if the processor is in user mode. If the decode function detects the illegal use of `eret` and `csrrw` instructions in user mode, the `iType` of the instruction will be set to a new value `NoPermission`, and the processor will report this error later.

## Processor

We have provided most of the processor code in `ExcepProc.bsv`, and you only need to fill out four places marked with the "`TODO`" comments:

1. A second argument for `decode` function.
2. Handle "unsupported instruction" exceptions: set `mepc` and `mcause`, push the new PRV and IE bits into the stack of `mstatus`, and chage PC to `mtvec`. You may want to use the `startExcep` method of `mkCsrFile`.
3. Handle system calls: system calls can be handled like an unsupported instruction exception.
4. Handle the `eret` instruction: pop the stack of `mstatus` and change PC to `mepc`. You may want to use the `eret` method of `mkCsrFile`.

## Test Programs

The test programs can be grouped into three classes: the assembly tests and benchmarks, which we have seen before, and a new group of programs that test your processor's exception-handling facilities.

### Old Programs

The assembly tests and benchmarks run in machine mode (these are said to "run bare-metal") and will not trigger exceptions. They can be compiled by going to `programs/assembly` and `programs/benchmarks` folders and running `make`.

### New Programs

The third class of programs deal with exceptions. These programs start in machine mode but immediately drop to user mode. All print functions are implemented as system calls, and the unsupported multiply instruction (`mul`) can be emulated in the software exception handler. The source for these programs also reside under the `programs/benchmarks` folder, but they are linked to libraries in the `programs/benchmarks/excep_common` folder (instead of `programs/benchmarks/common`).

To compile these programs, you can use the following commands:

```
$ cd programs/benchmarks
$ make -f Makefile.excep
```

The compilation results will appear in the `programs/build/excep` folder. (If you forget, you'll get an error like "ERROR: ../../programs/build/excep/vmh/median.riscv.vmh does not exit [sic], you need to first compile".)

These programs not only include the original benchmarks we have seen before, but also include two new programs:

- `mul_inst`: This is an alternative version of the original `multiply` benchmark, which directly uses the `mul` instruction.
- `permission`: This program executes the `csrrw` instruction in user mode, and should *fail*!

## Implementing Exceptions

> **Exercise 1 (40 Points):** Implement exceptions as described above on the processor in `ExcepProc.bsv`. You can build the processor by running
>
> ```
> build -v excep
> ```
>
> 
>
> in `scemi/sim`. We have provided the following scripts to run the test programs in simulation:
>
> 
>
> 1. `run_asm.sh`: run assembly tests in machine mode (without exceptions).
> 2. `run_bmarks.sh`: run benchmarks in machine mode (without exceptions).
> 3. `run_excep.sh`: run benchmarks in user mode (with exceptions).
> 4. `run_permit.sh`: run the `permission` program in user mode.
>
> Your processor should pass all the tests in the first three scripts (`run_asm.sh`, `run_bmarks.sh`, and `run_excep.sh`), but should report an error and terminate on the last script (`run_permit.sh`). Note that after you see the error message outputted by `bsim_dut` when running `run_permit.sh`, the software testbench `tb` is still running, so you'll need to hit `Ctrl-C` to terminate it.

> **Discussion Question 1 (10 Points):** In the spirit of the upcoming Thanksgiving holiday, list some reasons you are thankful you only have to do this lab on a one-cycle processor. To get you started: what new hazards would exceptions introduce if you were working on a pipelined implementation?

> **Discussion Question 2 (Optional):** How long did it take for you to finish this lab?

Remember to commit your code and `git push` when you're done.

------

© 2016 [Massachusetts Institute of Technology](http://web.mit.edu/). All rights reserved.









请输入需要翻译的文本。
