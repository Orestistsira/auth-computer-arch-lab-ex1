# auth-computer-arch-lab-ex1

**COMPUTER ARCHITECTURE**

**LAB-1**

*Tsirakis Orestis (AEM: 9995)*

*Kogia Iliana (AEM: 10090)*

---

1) Basic Parameters of Simulation System - script: Hello World


|CPU type|Minor CPU|parser.add\_argument("--cpu", type=str, choices=cpu\_types.keys(), default="atomic", …)|
| :- | :- | :- |
|CPU frequency|1GHz|<p>parser.add\_argument("--cpu-freq", type=str, default="1GHz")</p><p></p>|
|CPU cores|1|<p>parser.add\_argument("--num-cores", type=int, default=1, …)</p><p></p>|
|Memory Type|DDR3\_1600\_8x8|<p>parser.add\_argument("--mem-type", default="DDR3\_1600\_8x8", …)</p><p></p>|
|Memory Channels|2|<p>parser.add\_argument("--mem-channels", type=int, default=2, …)</p><p></p>|
|Memory Size|2GB|<p>parser.add\_argument("--mem-size", action="store", type=str, default="2GB", …)</p><p></p>|
|Caches|<p>L1 (L1I, L1D)</p><p>L2</p>|"minor" : (MinorCPU, devices.L1I, devices.L1D, devices.WalkCache, devices.L2)|

---
\
2) Validation of information from config.ini and config.json 

a)

|CPU frequency|<p>[system.cpu\_cluster.clk\_domain]</p><p>clock=1000 (1GHz -> 1000ns)</p>|
| :- | :- |
|CPU type|<p>[system.cpu\_cluster.cpus]</p><p>type=MinorCPU</p>|

b)

|sim\_seconds |0.000035 |# Number of seconds simulated|
| :- | :- | :- |
|sim\_insts                                                         	|5027  |# Number of instructions simulated (Count)|
|host\_inst\_rate |13049 |# Simulator instruction rate (inst/s)(Count/Second) |

c) Total number of “committed” instructions

- system.cpu\_cluster.cpus.committedInsts       	5027                   	# Number of instructions committed
- system.cpu\_cluster.cpus.op\_class\_0: :total		5831			#Class of committed instruction (contains both speculative and non-speculative plus some micro-operations)

Some instructions are executed but not completed due to speculation of instructions ahead.

all completed = 5027

all completed + speculative instructions + micro-operations = 5831

d)

L2 Cache: Overall Accesses = 474

|system.cpu\_cluster.l2.overall\_accesses::.cpu\_cluster.cpus.inst|327|# number of overall (read+write) accesses|
| :- | :- | :- |
|system.cpu\_cluster.l2.overall\_accesses::.cpu\_cluster.cpus.data|147|# number of overall (read+write) accesses|
|system.cpu\_cluster.l2.overall\_accesses::total|474  |# number of overall (read+write) accesses|
|system.cpu\_cluster.l2.overall\_misses::total |474 |# number of overall misses|
|system.cpu\_cluster.l2.overall\_miss\_rate::total |1|# miss rate for overall accesses|


L1 Cache: Overall Misses = 504

|system.cpu\_cluster.cpus.dcache.overall\_misses::total      	                 |177|# number of overall misses|
| :- | :- | :- |
|<p>system.cpu\_cluster.cpus.icache.overall\_misses::total          	</p><p></p>|327|# number of overall misses|
|system.cpu\_cluster.cpus.dcache.overall\_mshr\_hits::total       	                 |30|<p># number of overall MSHR hits (not going to l2 because we are already waiting for the result)</p><p></p>|


MSHR = missed status holding registers

Overall Misses of L1 – MSHR Hits of L1 = 504 - 30 = 474 = Overall Acceses of L2

The misses of L1 cache go to L2 cache as accesses except those found in mshr, because the data are already searched and coming to L1.

---
\
3)

MinorCPU:

* Αn in-order processor model with a fixed pipeline but configurable data structures and execute behaviour.
* Ιntended to be used to model processors with strict in-order execution behaviour and allows visualisation of an instruction’s position in the pipeline through the MinorTrace/minorview.py format/tool.
* Provides a framework for micro-architecturally correlating the model with a particular, chosen processor with similar capabilities.

SimpleCPU:

* A purely functional, in-order model that is suited for cases where a detailed model is not necessary.
* Cases include warm-up periods, client systems that are driving a host, or just testing to make sure a program works.

AtomicSimpleCPU:

* Is the version of SimpleCPU that uses atomic memory accesses.
* Uses the latency estimates from the atomic accesses to estimate overall cache access time.
* The AtomicSimpleCPU is derived from BaseSimpleCPU, and implements functions to read and write memory, and also to tick, which defines what happens every CPU cycle. It defines the port that is used to hook up to memory, and connects the CPU to the cache.

TimingSimpleCPU:

* Is the version of SimpleCPU that uses timing memory accesses.
* Stalls on cache accesses and waits for the memory system to respond prior to proceeding.
* Like the AtomicSimpleCPU, the TimingSimpleCPU is also derived from BaseSimpleCPU, and implements the same set of functions. It defines the port that is used to hook up to memory, and connects the CPU to the cache. It also defines the necessary functions for handling the response from memory to the accesses sent out.


~We expect that MinorCPU will be significantly faster than TimingSimpleCPU, because Minor uses pipeline for instruction executions, meaning it doesn’t need to stall other instructions while calculating result or waiting for memory accesses.

---

Script: ssort.c

a)

|<p>CPU clock = 1GHz</p><p>RAM = DDR3\_1600\_8x8</p>|Execution Time|
| :- | :- |
|MinorCPU|0.010283s = 10.283ms|
|TimingSimpleCPU|0.029522s = 29.522ms|

b)

As we expected MinorCPU is faster than TimingSimpleCPU with the same CPU clock and RAM, because as mentioned above Minor uses pipeline for instruction executions.

c)


|<p>CPU clock = 2GHz</p><p>RAM = DDR3\_1600\_8x8</p><p></p>|Execution Time|
| :- | :- |
|MinorCPU|<p>0.005157s = 5.157ms</p><p></p>|
|TimingSimpleCPU|<p>0.014775s = 14.775ms</p><p></p>|


|<p>CPU clock = 4GHz</p><p>RAM = DDR3\_1600\_8x8</p><p></p>|Execution Time|
| :- | :- |
|MinorCPU|<p>0.002594s = 2.594ms</p><p></p>|
|TimingSimpleCPU|<p>0.007402s = 7.402ms</p><p></p>|

\
~For the same RAM type, when we double the CPU frequency the execution time drops to half as exptected.


|<p>CPU clock = 1GHz</p><p>RAM = DDR4\_2400\_16x4</p><p></p>|Execution Time|
| :- | :- |
|MinorCPU|<p>0.010282s = 10.282ms</p><p></p>|
|TimingSimpleCPU|<p>0.029522s = 29.522ms</p><p></p>|

\
~For the same CPU frequency, when we alter the RAM type we do not observe any significant change, because the program we run is not memory intensive and does not have many memory accesses.

Referances:

[https://www.gem5.org/](https://www.gem5.org/)

---
\
Κριτική εργαστηρίου: Θεωρούμε ότι το πρώτο εργαστήριο αποτελεί μία καλή αφετηρία για τον εξομοιωτή gem5. Είναι αρκετά επεξηγηματικό ως προς τα ζητούμενα και τη γενική χρησιμότητα του gem5, βέβαια υπήρξε μια αρχική δυσκολία στην εύρεση των αποτελεσμάτων στα αρχεία. Στη συνέχεια με την εξοικείωση με το πρώτο script (hello world) η εύρεση των παραμέτρων για το δεύτερο script (ssort) έγινε αρκετά πιο εύκολη και οικεία. 







