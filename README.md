Download Link: https://assignmentchef.com/product/solved-eth263-2210-lab-1-simulating-and-exploring-cache-behavior
<br>
<h1>1. Introduction</h1>

In this lab, you will extend a <em><sub>timing simulator </sub></em>(written in C) to model instruction/data caches. Unlike the

RTL that you worked with in the Design of Digital Circuits course, a <em><sub>timing simulator </sub></em>is <em><sub>not </sub></em>a direct or synthesizable implementation of a processor. Rather, it is a higher-level, abstract model designed to allow quick architectural exploration. Describing and simulating hardware at a higher level of abstraction allows the designer to quickly see how design choices (e.g., caches or branch predictors) would impact performance. We will give you the base simulator, which models a simple MIPS processor. We will also fully specify the behavior of the caches. Your job is to extend the simulator so that it implements the caches as specified.

<h1>2. Timing Simulator</h1>

We are not constrained to logic-level implementation in a C-based timing simulator. Our main goal is to compute the <em><sub>number of cycles </sub></em>that a program requires to execute on the simulated processor. Because of this, many simplifications are possible:

<ul>

 <li>We do not actually need to model control logic and datapath details in each block of the processor.</li>

 <li>We only need to write code for each stage that performs the relatively high-level function of that pipeline stage (read the register file, access memory, etc.).</li>

 <li>In general, the simulator’s algorithms and structures <em><sub>do not </sub></em>need to <em><sub>exactly </sub></em>match the processor’s algorithms and structures, as long as the <em><sub>result </sub></em>is the same.</li>

</ul>

While we no longer have a low-level implementation, and thus cannot determine the critical path (or other cost metrics that we care about, e.g., area taken on a silicon chip, power consumed during operation), we can know how many cycles a program would take (assuming we model the cycle-level solution accurately).

<h1>3. Task 1/2: Additions to the Baseline Timing Simulator</h1>

Your goal is to <em><sub>implement </sub></em>the timing simulator so that it models a MIPS machine with instruction/data caches accurate to the specifications we provide (below). In the following, we will fully specify the microarchitecture of the MIPS machine that you will simulate.

<h2>3.1. Instruction Cache</h2>

The <em><sub>instruction cache </sub></em>is accessed every cycle by the fetch stage.

Organization. It is a four-way set-associative cache that is 8 KB in size with 32-byte blocks (this implies that the cache has <sub>64 </sub>sets). When accessing the cache, the set index is calculated using bits PC[10:5].

Miss Timing. When the fetch stage <em><sub>misses </sub></em>in the instruction cache, the block must be retrieved from main memory. An access to main memory takes <sub>50 </sub>cycles. On the 50th cycle, the new block is inserted into the cache. In total, an instruction cache miss stalls the pipeline for 50 cycles.

Replacement. When a new block is retrieved from main memory, it is inserted into the appropriate set within the instruction cache. If any way within the set is empty (i.e., invalid), the new block is simply inserted into the invalid way. However, if none of the ways in the set are empty, the new block <em><sub>replaces </sub></em>the <em><sub>least-recently-used </sub></em>block in the set. For both cases, the new block becomes the <em><sub>most-recently-used </sub></em>block.

<h2>3.2. Data Cache</h2>

The <em><sub>data cache </sub></em>is accessed whenever a load or store instruction is in the memory stage.

Organization. It is an eight-way set-associative cache that is 64 KB in size with 32 byte blocks (this implies that the cache has <sub>256 </sub>sets). When accessing the cache, the set index is calculated using bits [12:5] of the data address that is being loaded/stored.

Miss Timing &amp; Replacement. Miss timing and replacement of the data cache are identical to those of the instruction cache.

Handling Stores. Both load and store misses stall the pipeline for 50 cycles. They both retrieve a new block from main memory and insert it into the cache.

Dirty Evictions. When a “dirty” block is replaced by a new block from main memory, it must be written back into main memory. For the purpose of this lab, we will assume that such dirty evictions are handled <em>instantaneously </em>– i.e., they are written immediately into main memory in the same cycle as when the new block is inserted into the cache.

<h2>3.3. Assumptions about Instruction &amp; Data Caches</h2>

<ul>

 <li>Assume that both caches are initially empty (i.e., all blocks are invalid).</li>

 <li>In both caches, every block has a separate tag that stores information about the block: e.g., address, valid, recency, etc. Tags are initialized to 0.</li>

 <li>Assume that the program that runs on the processor <em><sub>never </sub></em>modifies its own code (referred to as selfmodifying code): a given block <em><sub>cannot </sub></em>reside in both the caches.</li>

</ul>

<h1>4. Task 2/2: Cache Exploration</h1>

Your second task is to evaluate the performance characteristics of the caches that you implemented as part of Task 1. You must evaluate the following design characteristics:

<ol>

 <li>Cache size, block size, associativity: a sweep of <em>cache parameters</em>. You should write a set of benchmarks that use significant amounts of memory (for example, accessing a large array in streaming or random patterns), and run your simulator to measure IPC for various cache parameters. Show how changing the associativity, block size, and cache size affect performance.</li>

 <li>Replacement and insertion policies: an exploration of cache replacement and/or insertion policies. The <em><sub>cache replacement policy </sub></em>specifies which cache block in a set is replaced when a new block is inserted into the cache. The <em><sub>cache insertion policy </sub></em>specifies where in the list of blocks the new block is placed. Up to now, we have used a replacement policy that evicts (replaces) the least-recently-used block, and an insertion policy that places new blocks at the most-recently-used position. However, other replacement and insertion policies have been studied, and some have been shown to achieve significantly better performance (fewer cache misses) for certain access patterns [1, 2]. You should experiment with a variety of test programs and optimize the cache replacement/insertion policy.</li>

 <li><sub>Other (extra credit): </sub>Optionally, you may also choose to experiment with other aspects of the cache. For example, using more sophisticated hashing functions to map cache blocks to cache sets and/or using more than one hashing function [3]. Implementing a victim cache is another possibility [4]. Since this part is open-ended, the instructor reserves the amount of extra credit that can be obtained, but up to 25% extra credit is possible depending on the difficulty of the optimization and the goodness of the resulting design and implementation.</li>

</ol>

Note that an important part of evaluating the performance characteristics is comparing against a sensible baseline. For example, if the baseline does not model the main memory latency while your design does, a comparison of cycle counts will not be meaningful. It is your job to discuss and compare against appropriate baselines for each design that you evaluate.

Please write a report (report cache.pdf) that summarizes:

i Your observations on the effect of each cache parameter. ii Your findings on cache replacement/insertion policies. iii Any other optimizations you implement.

Your report does not need to be more than four pages, but feel free to use more pages to present schematics, data, and graphs. Please also submit the version of your simulator (src/) that implements the best performing cache design(s). This version of the simulator and the report should be submitted under their own folder within the same tarball that you submit (see Section 7 for a detailed list of what to submit).

<h1>5. Lab Resources</h1>

<h2>5.1. Source Code</h2>

Do <sub>NOT </sub>modify any files or folders unless explicitly specified in the list below.

<ul>

 <li>Makefile</li>

 <li>py: Script that runs your simulator and compares it against the baseline simulator. You may need to modify this script for your own debugging. However, your submission <sub>must </sub>work with the <sub>unmod</sub>ified version.</li>

 <li><sup>src/</sup>: Source code (Modifiable; feel free to add more files)

  <ul>

   <li>c: Your simulator (Modifiable)</li>

   <li>h: Your simulator (Modifiable)</li>

   <li>h: MIPS related pound defines</li>

   <li>c: Interactive shell for your simulator – shell.h: Interactive shell for your simulator</li>

  </ul></li>

 <li>inputs/: Example test inputs for your simulator (Modifiable; feel free to add more files)</li>

</ul>

<h2>5.2. Makefile</h2>

We provide a Makefile that automates the compilation and verification of your simulator.

The first time you use the Makefile you should compile the baseline simulator:

$ make basesim

This will generate basesim, which is the baseline simulator corresponding to the code we provide. You can use it to verify the output of a program you run on your simulator. Note that the output of a program should always match the output obtained by running the program on the baseline simulator. However, the execution time of a program on the two simulators will not be same after your changes on the caches. To compile your simulator:

$ make

To compile your simulator and check it against the baseline simulator using one or more test inputs:

$ make run INPUT=inputs/inst/addiu.x

$ make run INPUT=inputs/inst/*.x

$ make run

You may modify the Makefile or the Python script run.py (which compares the output of sim and basesim) in order to debug your code. However, your final submitted version must contain the original <strong>Makefile </strong>and <strong>run.py</strong>.

<h1>6. Getting Started &amp; Tips</h1>

<h2>6.1. The Goal</h2>

We provide you with a skeleton of the timing simulator that models a five-stage MIPS pipeline: pipe.c and pipe.h. As it is, the simulator is already architecturally correct: it can correctly execute any arbitrary MIPS program that only uses the implemented instructions.<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a> When the simulator detects data dependences, it correctly handles them by stalling and/or bypassing. When the simulator detects control dependences, it correctly handles them by stalling the pipeline as necessary.

By executing the following command, you can see that your simulator (sim) does indeed have identical architectural outputs (e.g., register values) as the baseline simulator (basesim) for all the test inputs that we provide in inputs/.

$ make run

Your job is to model accurately the timing effects of the caches and the main memory in your timing simulator.

<h2>6.2. Studying the Timing Simulator</h2>

Please study pipe.c and pipe.h in detail.

The simulator models each pipeline stage as a separate function – e.g., pipe stage fetch(). The simulator models the state of the pipeline as a collection of pointers to Pipe Op structures (defined in pipe.h). Each Pipe Op represents one instruction in the pipeline by storing all of the necessary information about the instruction that is needed by the simulator. A Pipe Op structure is allocated when an instruction is fetched. It then flows through the pipeline and eventually arrives at the last stage (writeback), where it is deallocated once the instruction completes. To elaborate, each stage receives a Pipe Op from the previous stage, processes the Pipe Op, and passes it down to the next stage. The simulator models pipeline stalls by stopping the flow of Pipe Op structures and pipeline flushes by deallocating the Pipe Op structures at different stages.

6.3. Tips • Please do not distribute the provided program files. These are for exclusive individual use of each student of the Computer Architecture course. Distribution and sharing violates the copyright of the software provided to you. • Read this handout in detail.

<ul>

 <li>If needed, please ask questions to the TAs using the online Q&amp;A forum in <sub>Piazza (</sub><a href="https://piazza.com/class/kf1a8mpvz6hdt"><strong><sub>https://piazza. </sub></strong></a><a href="https://piazza.com/class/kf1a8mpvz6hdt"><strong>com/class/kf1a8mpvz6hdt</strong></a><a href="https://piazza.com/class/kf1a8mpvz6hdt">)</a> under the “Lab 1” tag.</li>

 <li>If you encounter a technical problem, please first read the error messages. A quick web search can usually solve many debugging issues and error messages.</li>

 <li>Use debugging tools. Learn how to use GDB/LLDB, if you are not already familiar. There is no substitute for stepping through code and peeking at partial values when trying to figure out where your code is going wrong.</li>

 <li>One reasonable way to approach this lab is to first write a generic implementation of a set-associative cache, and then plug it into both the fetch stage (instruction cache) and the memory stage (data cache). • Write additional test cases. You can use online tools (e.g., <a href="https://alanhogan.com/asu/assembler.php">https://alanhogan.com/asu/assembler. </a><a href="https://alanhogan.com/asu/assembler.php">php</a><a href="https://alanhogan.com/asu/assembler.php">,</a> <a href="http://shell-storm.org/online/Online-Assembler-and-Disassembler/">http://shell-storm.org/online/Online-Assembler-and-Disassembler/</a><a href="http://shell-storm.org/online/Online-Assembler-and-Disassembler/">)</a> to write and assemble your own MIPS-32 test cases.</li>

</ul>

<h2>6.4. Background and Reference Material</h2>

Here are some reference materials to help you get started if you feel unfamiliar or uncomfortable with the topics that this assignment deals with.

<ul>

 <li>Digitaltechnik Spring 2020 course: <a href="https://safari.ethz.ch/digitaltechnik/spring2020/doku.php?id=start">https://safari.ethz.ch/digitaltechnik/spring2020/doku. </a><a href="https://safari.ethz.ch/digitaltechnik/spring2020/doku.php?id=start">php?id=start</a><a href="https://safari.ethz.ch/digitaltechnik/spring2020/doku.php?id=start">.</a> Relevant lectures include:

  <ul>

   <li>Von Neumann Model, ISA, LC-3, and MIPS <a href="https://safari.ethz.ch/digitaltechnik/spring2020/lib/exe/fetch.php?media=digitaldesign-2020-lecture9-lc3andmips-beforelecture.pdf">(slides,</a> <a href="https://www.youtube.com/watch?v=RwQdPdGN9l0">video)</a></li>

   <li>L10a: Instruction Set Architecture <a href="https://safari.ethz.ch/digitaltechnik/spring2020/lib/exe/fetch.php?media=digitaldesign-2020-lecture10a-lc3andmips-beforelecture.pdf">(slides,</a> <a href="https://www.youtube.com/watch?v=FUleWv4_rOg">video)</a></li>

   <li>L11: Microarchitecture I <a href="https://safari.ethz.ch/digitaltechnik/spring2020/lib/exe/fetch.php?media=onur-digitaldesign-2020-lecture11-microarchitecture-i-beforelecture.pdf">(slides,</a> <a href="https://www.youtube.com/watch?v=uKchSidT_Uo">video)</a></li>

   <li>L12: Microarchitecture II <a href="https://safari.ethz.ch/digitaltechnik/spring2020/lib/exe/fetch.php?media=onur-digitaldesign-2020-lecture12-microarchitecture-ii-beforelecture.pdf">(slides,</a> <a href="https://www.youtube.com/watch?v=7R-NvVms3uY">video)</a></li>

   <li>L13: Pipelining <a href="https://safari.ethz.ch/digitaltechnik/spring2020/lib/exe/fetch.php?media=onur-digitaldesign-2020-lecture13-pipelining-beforelecture.pdf">(slides,</a> <a href="https://www.youtube.com/watch?v=_WNdzZB0RJA">video)</a></li>

   <li>L21b: Memory Hierarchy and Caches <a href="https://safari.ethz.ch/digitaltechnik/spring2020/lib/exe/fetch.php?media=onur-digitaldesign-2020-lecture21b-memory-hierarchy-and-caches-beforelecture.pdf">(slides,</a> <a href="https://www.youtube.com/watch?v=mZ7CPJKzwfM">video)</a></li>

   <li>L22: More Caches <a href="https://safari.ethz.ch/digitaltechnik/spring2020/lib/exe/fetch.php?media=onur-digitaldesign-2020-lecture22-more-caches-beforelecture.pdf">(slides,</a> <a href="https://www.youtube.com/watch?v=TsxQPLMXT60">video)</a></li>

  </ul></li>

 <li>Harris and Harris textbook [5] – Chapter 8.3: Caches

  <ul>

   <li>Chapter 7: Microarchitecture</li>

  </ul></li>

</ul>


