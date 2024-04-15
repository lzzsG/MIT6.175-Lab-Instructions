# Project Part 1: Store Queue

> The first part of the project has no explicit due date.
>
> However, your entire project will be due at project presentations to be held **Wednesday, December 14, at 3 PM EST**.

In the first part of the final project, we will add store queue to the blocking data cache (D$) designed in Lab 7.

## Cloning the project code

Because this is a project to be done in pairs, you will need to have first contacted me with the usernames of the people in your group. To clone your Git repository, do the following command, where `${PERSON1}` and `${PERSON2}` are your Athena usernames and `${PERSON1}` occurs alphabetically before `${PERSON2}`:

```
$ git clone /mit/6.175/groups/${PERSON1}_${PERSON2}/project-part-1.git project-part-1
```

## Refining the blocking cache

It only makes sense to implement a store queue for the data cache, but we want to keep the design of the instruction cache (the I$) the same as the one from Lab 7. Therefore, we'll need to separate the design of the data cache and instruction cache. `src/includes/CacheTypes.bsv` contains the new cache interfaces, even though they look identical:

```
interface ICache;
  method Action req(Addr a);
  method ActionValue#(MemResp) resp;
endinterface

interface DCache;
  method Action req(MemReq r);
  method ActionValue#(MemResp) resp;
endinterface
```

You will implement your I$ in `ICache.bsv`, and your D$ in `DCache.bsv`.

### Shortcomings of the lab 7 cache design

In Lab 7, the `req` method of the cache checks the tag array, determines if the access was a cache hit or miss, and performs the actions needed to handle either case. However, if you look at the compilation output of Lab 7, you will find the rule for the memory stage of the processor conflicts with several rules in the D$ that replace cache lines, send memory requests and receive memory responses. These conflicts arise because the compiler cannot accurately determine when the cache's data arrays, tag arrays, and state registers will be updated when they are manipulated in the `req` method that your processor calls.

The compiler also treats the memory stage rule as "more urgent" than the D$ rules, so when the memory stage fires, the D$ rules cannot fire in the same cycle. Such conflicts will not affect the correctness of the cache design, but they may hurt performance.

### Resolving rule conflicts

To eliminate these conflicts, we add a one-element bypass FIFO called `reqQ` to the D$. All the requests from the processor will first go into `reqQ`, get processed from the D$, and dequeued. To be more specific, the `req` method simply enqueues the incoming request into `reqQ`, and we will create a new rule, say `doReq`, to do the work originally done in the `req` method (i.e. dequeue a request from `reqQ` for processing in the absence of other requests).

The explicit guard of the `doReq` rule will make it mutually exclusive with the other rules in the D$ and eliminate theses conflicts. Since `reqQ` is a bypass FIFO, the hit latency of D$ is still one cycle.

> **Exercise 1 (10 Points):** Integrate the refined D$ (with bypass FIFO) into the processor. Here's a brief outline of what you'll need to do:
>
> 1. Copy `Bht.bsv` from Lab 7 to `src/includes/Bht.bsv`.
> 
> 2. Complete the processor pipeline in`src/Proc.bsv` . You can complete the partially-completed code with the code you wrote in`WithCache.bsv`in Lab 7.
> 
> 3. Implement the I$ in `src/includes/ICache.bsv`. You can directly use the cache design in Lab 7.
>
> 4. Implement the refined D$ design in the `mkDCache` module in `src/includes/DCache.bsv`.
>
> 5. Build the processor by running
>
>    ```
>    $ build -v cache
>    ```
>    under the` scemi/sim` folder. This time, you should not see any warnings related to rule conflicts within`mkProc`.
>
> 6. Test the processor by running
>
>    ```
>    $ ./run_asm.sh cache
>    ```
>
>    and
>
>    ```
>    $ ./run_bmarks.sh cache
>    ```
>
>    under the` scemi/sim`folder. The standard output of bluesim will be redirected to log files under the`scemi/sim/logs`folder. For the new assembly test`cache_conflict.S`, the IPC should be around 0.9. If you get an IPC much less than 0.9, there's probably a mistake somewhere in your code.

> **Discussion Question 1 (5 Points):** Explain why the IPC of assembly test `cache_conflict.S` is so high even though there is a store miss in every loop iteration. The source code is located in `programs/assembly/src`.

## Adding a store queue

Now, we'll add a store queue to the D$.

### The store queue module interface

We have provided a parametrized implementation of an *n*-entry store queue in `src/includes/StQ.bsv`. The type of each store queue entry is just the `MemReq` type, and the interface is:

```
typedef MemReq StQEntry;
interface StQ#(numeric type n);
  method Action enq(StQEntry e);
  method Action deq;
  method ActionValue#(StQEntry) issue;
  method Maybe#(Data) search(Addr a);
  method Bool notEmpty;
  method Bool notFull;
  method Bool isIssued;
endinterface
```

The store queue is very similar to a conflict-free FIFO, but it has some unique interface methods.

- method `issue`: returns the oldest entry of the store queue (i.e. `FIFO.first`), and sets a status bit inside the store queue. Later calls to the `issue` method will be blocked if this status bit is not cleared.
- method `deq`: remove the oldest entry from the store queue, and clears the status bit set by the `issue` method.
- method `search(Addr a)`: returns the data field of the youngest entry in the store queue for which the address field is equal to the method argument `a`. If there is no entry in the store queue that writes to address `a`, the method will return `Invalid`.

You can look at the implementation of this module to better understand the behavior of each interface method.

### Inserting into the store queue

Let `stq` denote the store queue instantiated inside the D$. As mentioned in the class, a store request from the processor should be placed into `stq`. Since we have introduced the bypass FIFO `reqQ` in the D$, we should enqueue the store request into `stq` after we dequeue it from `reqQ`. Note that the store request cannot be directly enqueued into `stq` in the `req` method of D$, because this may cause a load to bypass a younger store's value. In other words, all requests from the processor are still first enqueued into `reqQ`.

It should also be noted that placing a store into `stq` can happen in parallel with almost all other operations, such as processing a miss, because the `enq` method of the store queue is designed to be conflict-free with other methods.

### Issuing from the store queue

If the cache isn't currently processing any requests, we can process the oldest entry of the store queue or an incoming load request at `reqQ.first`. The load request from the processor should have priority over the store queue. That is, if `stq` has valid entries but `reqQ.first` has a load request, then we process the load request. Otherwise, we call the `issue` method of `stq` to get the oldest store to process.

Note that a store is dequeued from the store queue when the store commits (i.e. writes data to cache), instead of when processing starts. This enables some optimizations we will implement later (but not in this section). The `issue` and `dequeue` methods are designed to be able to be called in the same rule, so that we can call both of them when the store hits in the cache.

It should also be noted that issuing stores from the store queue should not be blocked when `reqQ.first` is a store request. Otherwise, the cache may deadlock.

> **Exercise 2 (20 Points):** Implement the blocking D$ with store queue in the `mkDCacheStQ` module in `src/includes/DCache.bsv`. You should use the numeric type `StQSize` already defined in `CacheTypes.bsv` as the size of the store queue. You can build the processor by running
>
> ```
> $ build -v stq
> ```
>
> under the `scemi/sim` folder, and test it by running
>
> ```
> $ ./run_asm.sh stq
> ```
>
> and
>
> ```
> $ ./run_bmarks.sh stq
> ```
>

To avoid conflicts due to low scheduling effort on the compiler's part, we suggest splitting the `doReq` rule into two rules: one for stores and the other for loads.

For the new assembly test `stq.S`, the IPC should be above 0.9 since the store miss latency is almost completely hidden by the store queue. However, you may not see any performance improvement for the benchmark programs.

## Load hit under store miss

Although the store queue significantly improves the performance of the assembly test `stq.S`, it fails to make any difference for the benchmark programs. To understand the limitations of our cache design, let's consider a case in which a store instruction is followed by an add instruction and then a load instruction. In this case, the store will begin processing in the cache before the load request is sent to the cache. If the store incurs a cache miss, the load will be blocked even if it could hit in the cache. Namely, the store queue fails to hide the store miss latency.

In order to get better performance without complicating the design by too much, we could allow a load hit to happen in parallel with a *store* miss. Specifically, let's suppose `reqQ.first` is a load request. If there is no other request being processed by the cache, we could definitely process `reqQ.first`. However, if a store request is waiting for the response from memory that has not yet arrived, we could attempt to process the load request by checking whether it hits in the store queue or cache. If the load hits in either the store queue or the cache, we can dequeue it from `reqQ`, forward the data from the store queue or read it from the cache, and return the value of the load to the processor. If the load is a miss, we take no further action and just keep it in `reqQ`.

Note that there is no structural hazard by allowing a load hit because the pending store miss doesn't access the cache or its state. We should also note that a load hit *cannot* happen in parallel with a *load* miss, since we don't want the load responses to arrive out-of-order.

For your convenience, we have added an additional method called `respValid` to the `WideMem` interface defined in `CacheTypes.bsv`. This method will return `True` when there is a response available from `WideMem` (i.e. it is equal to the guard of the `resp` method of `WideMem`).

> **Exercise 3 (10 Points):** Implement the blocking D$ with store queue that allows load hits under store misses in the `mkDCacheLHUSM` module in `src/includes/DCache.bsv`. You can build the processor by running
>
> ```
> $ build -v lhusm
> ```
>
> under the `scemi/sim` folder, and test it by running
>
> ```
> $ ./run_asm.sh lhusm
> ```
>
> and
>
> ```
> $ ./run_bmarks.sh lhusm
> ```
>
> You should be able to see some improvement in the performance of some benchmark programs.
>

> **Discussion Question 2 (5 Points):** In un-optimized assembly code, a program may write to memory only to read it in the very next instruction:
>
> ```
> sw  x1, 0(x2)
> lw  x3, 0(x2)
> add x4, x3, x3
> ```
>
> This frequently happens when a program saves its arguments to a subroutine on the stack. Instead of writing out a register's value to memory, an *optimizing compiler* (GCC, for instance) can keep the value in a register to speed up accesses to this data. How can this behavior of an optimizing compiler affect what you have just designed? Are store queues still important?
>

> **Discussion Question 3 (5 Points):** How much improvement do you see in the performance of each benchmark compared to the cache designs in Exercises 1 and 2?

------

© 2016 [Massachusetts Institute of Technology](http://web.mit.edu/). All rights reserved.
