# MIT-6.175 Introduction/Contents

## What is 6.175?

6.175 teaches the fundamental principles of computer architecture via implementation of different versions of pipelined machines with caches, branch predictors and virtual memory. Emphasis on writing and evaluating architectural descriptions that can be both simulated and synthesized into real hardware or run on FPGAs. The use and design of test benches. Weekly labs. Intended for students who want to apply computer science techniques to complex hardware design.

Topics include combinational circuits including adders and multipliers, multi-cycle and pipelined functional units, RISC Instruction Set Architectures (ISA), non-pipelined and multi-cycle processor architectures, 2- to 10-stage in-order pipelined architectures, processors with caches and hierarchical memory systems, TLBs and page faults, I/O interrupts.

### Instructors

- [Arvind](http://csg.csail.mit.edu/Users/arvind)
- Quan Nguyen (TA)

### Lectures

MWF 3:00 pm, [34-302](http://whereis.mit.edu/map-jpg?mapterms=34-302).

------

### Lab Assignments Directory

- [MIT-6.175 Introduction/Contents](./en/MIT6.175.md)
- [Lab 0: Getting Started](./en/MIT6.175/Lab0.md)
- [Lab 1: Multiplexers and Adders](./en/MIT6.175/Lab1.md)
- [Lab 2: Multipliers](./en/MIT6.175/Lab2.md)
- [Lab 3: FFT Pipeline](./en/MIT6.175/Lab3.md)
- [Lab 4: N-Element FIFOs](./en/MIT6.175/Lab4.md)
- [Lab 5: RISC-V Introduction - Multi-cycle and Two-Stage Pipelines](./en/MIT6.175/Lab5.md)
- [Lab 6: RISC-V Processor with 6-Stage Pipeline and Branch Prediction](./en/MIT6.175/Lab6.md)
- [Lab 7: RISC-V Processor with DRAM and Caches](./en/MIT6.175/Lab7.md)
- [Lab 8: RISC-V Processor with Exceptions](./en/MIT6.175/Lab8.md)

#### Project

- [Part 1: Store Queue](./en/MIT6.175/project1.md)
- [Part 2: Cache Coherence](./en/MIT6.175/project2.md)

------

### Schedule

Please check back frequently as this schedule may change.

This calendar is also available on [Google Calendar](https://www.google.com/calendar/embed?src=d8i2pcp6er3o1h01idvsfhoktc@group.calendar.google.com&ctz=America/New_York).

| Week         | Date                                                         | Description                                                  | Downloads                                                    |
| :----------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 1            | Wed, Sept 7                                                  | Lecture 1: Introduction                                      | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L01-CCAwoPictures.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L01-CCAwoPictures.pdf) |
|  | Fri, Sept 9  | Lecture 2: Combinational Circuits **[Lab 0](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab0-getting-started.html) out, [Lab 1](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab1-multiplexers-adders.html) out** | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L02-CombinationalCkts.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L02-CombinationalCktsPrint.pdf) |
| 2            | Mon, Sept 12                                                 | Lecture 3: Combinational Circuits 2                          | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L03-CombinationalCkts-2.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L03-CombinationalCkts-2.pdf) |
|  | Wed, Sept 14 | Lecture 4: Sequential Circuits                               | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L04-SequentialCircuits.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L04-SequentialCircuits.pdf) |
|  | Fri, Sept 16 | Lecture 5: Sequential Circuits 2 **Lab 1 due, [Lab 2](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab2-multipliers.html) out** | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L05-Folded-Combinational-Circuits-revised.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L05-Folded-Combinational-Circuits-revised.pdf) |
| 3            | Mon, Sept 19                                                 | Lecture 6: Pipelining Combinational Circuits                 | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L06-CombinationalPipelines.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L06-CombinationalPipelines.pdf) |
|  | Wed, Sept 21 | Lecture 7: Well-Formed BSV Programs Ephemeral History Registers | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L07-EHRs-Multirule-Systems-and-Concurrency.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L07-EHRs-Multirule-Systems-and-Concurrency.pdf) |
|  | Fri, Sept 23 | **No classes: Student Holiday (Fall Career Fair)** **[Lab 3](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab3-fft.html) out** |                                                              |
| 4            | Mon, Sept 26                                                 | Lecture 8: Multirule Systems and Concurrent Execution of Rules **Lab 2 due** | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L08-Multirule-Systems-and-Concurrency.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L08-Multirule-Systems-and-Concurrency.pdf) |
|  | Wed, Sept 28 | Lecture 9: Guards                                            | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L09-Guards.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L09-Guards.pdf) |
|  | Fri, Sept 30 | Tutorial 1: Bluespec                                         | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T01-BSV.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T01-BSV.pdf) |
| 5            | Mon, Oct 3                                                   | Lecture 10: Non-pipelined Processors **[Lab 4](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab4-fifo.html) out** | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L10-NonPipelinedProcessors.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L10-NonPipelinedProcessors.pdf) |
|  | Wed, Oct 5   | Lecture 11: Non-pipelined and Pipelined Processors **Lab 3 due** | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L11-Nonpipelined-Processors-2.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L11-Nonpipelined-Processors-2.pdf) |
|  | Fri, Oct 7   | Tutorial 2: Advanced Bluespec                                | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T02-Advanced-BSV.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T02-Advanced-BSV.pdf) |
| 6            | Mon, Oct 10                                                  | **No classes: Indigenous Peoples' Day / Columbus Day**       |                                                              |
|  | Tue, Oct 11  | **[Lab 5](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab5-riscv-intro.html) out** |                                                              |
|  | Wed, Oct 12  | Lecture 12: Control Hazards **Lab 4 due**                    | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L12-Control-Hazards.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L12-Control-Hazards.pdf) |
|  | Fri, Oct 14  | Tutorial 3: RISC-V Processor RISC-V and Debugging            | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T03-RISCV-debug.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T03-RISCV-debug.pdf) |
| 7            | Mon, Oct 17                                                  | Lecture 13: Data Hazards                                     | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L13-Data-Hazards.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L13-Data-Hazards.pdf) |
|  | Wed, Oct 19  | Lecture 14: Multistage Pipelines **[Lab 6](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab6-riscv-pipeline.html) out** | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L14-MultistagePipelines.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L14-MultistagePipelines.pdf) |
|  | Fri, Oct 21  | Tutorial 4: Debug Epochs and Scoreboards Lab 5 due           | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T04-Epochs.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T04-Epochs.pdf) |
| 8            | Mon, Oct 24                                                  | Lecture 15: Branch Prediction **Lab 5 due**                  | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L15-BranchPrediction.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L15-BranchPrediction.pdf) |
|  | Wed, Oct 26  | Lecture 16: Branch Prediction 2                              | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L16-BranchPrediction-2.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L16-BranchPrediction-2.pdf) |
|  | Fri, Oct 28  | Tutorial 5: Epochs and Branch Predictors Epochs, Debugging, and Caches | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T05-Caches-Exceptions.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T05-Caches-Exceptions.pdf) |
| 9            | Mon, Oct 31                                                  | Lecture 17: Caches                                           | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L17-Caches-1.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L17-Caches-1.pdf) |
|  | Wed, Nov 2   | Lecture 18: Caches 2 **[Lab 7](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab7-riscv-caches.html) out** | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L18-Caches-2.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L18-Caches-2.pdf) |
|  | Fri, Nov 4   | Tutorial 6: Caches and Exceptions Lab 6 due                  | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T06-Caches-Exceptions.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T06-Caches-Exceptions.pdf) |
| 10           | Mon, Nov 7                                                   | Lecture 19: Exceptions **Lab 6 due**                         | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L19-Exceptions.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L19-Exceptions.pdf) |
|  | Wed, Nov 9   | Lecture 20: Virtual Memory                                   | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L20-VirtualMemory.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L20-VirtualMemory.pdf) |
|  | Fri, Nov 11  | **No classes: Veterans Day**                                 |                                                              |
| 11           | Mon, Nov 14                                                  | Lecture 21: Virtual Memory and Exceptions Lab 8 out          | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L21-VirtualMemory-2.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L21-VirtualMemory-2.pdf) |
|  | Wed, Nov 16  | Lecture 22: Cache Coherence Lab 7 due                        | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L22-CacheCoherence.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L22-CacheCoherence.pdf) |
|  | Thu, Nov 17  | **[Lab 8](http://csg.csail.mit.edu/6.175/archive/2016/labs/lab8-riscv-exceptions.html) out** |                                                              |
|  | Fri, Nov 18  | Tutorial 7: Project Overview **Lab 7 due, [Project Part 1](http://csg.csail.mit.edu/6.175/archive/2016/labs/project-part-1.html) out** | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T07-Project-Overview.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T07-Project-Overview.pdf) |
| 12           | Mon, Nov 21                                                  | Lecture 23: Sequential Consistency                           | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L23-Sequential-Consistency.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/L23-Sequential-Consistency.pdf) |
|  | Wed, Nov 23  | Tutorial 8: Project Part 2: Coherence **Cancelled: (early) Thanksgiving** Lab 8 due |                                                              |
|  | Fri, Nov 25  | **No classes: Thanksgiving** **Lab 8 due**                   |                                                              |
| 13           | Mon, Nov 28                                                  | **No classes: Work on project** Project Part 2 out           |                                                              |
|  | Wed, Nov 30  | **No classes: Work on project**                              |                                                              |
|  | Thu, Dec 1   | **[Project Part 2](http://csg.csail.mit.edu/6.175/archive/2016/labs/project-part-2.html) out** |                                                              |
|  | Fri, Dec 2   | No classes: Work on project Tutorial 8: Project Part 2: Coherence | [[pptx]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T08-Project-Coherence.pptx) [[pdf]](http://csg.csail.mit.edu/6.175/archive/2016/lectures/T08-Project-Coherence.pdf) |
| 14           | Mon, Dec 5                                                   | **No classes: Work on project**                              |                                                              |
|  | Wed, Dec 7   | **No classes: Work on project**                              |                                                              |
|  | Fri, Dec 9   | **No classes: Work on project**                              |                                                              |
| 15           | Mon, Dec 12                                                  | **No classes: Work on project**                              |                                                              |
|  | Wed, Dec 14  | **Last day of classes** **Project presentations**            |                                                              |

---

© 2016 [Massachusetts Institute of Technology](http://web.mit.edu/). All rights reserved.
