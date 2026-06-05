# CS202 Computer Organization Mock Final Exams

Scope: Lecture 09 to Lecture 14, based on the lecture slides and RISC-V32.

Format per paper: 110 points, 120 minutes, English.

Difficulty target: medium to upper-medium. A well-prepared student who knows the slides should be able to score around 80/110; higher scores require accurate calculations and careful hazard/schedule reasoning.

---

# Mock Exam A

## Part I. True / False (20 points, 2 points each)

Write T for true and F for false.

1. Pipelining mainly improves instruction throughput rather than the latency of one single instruction.
2. A structural hazard happens when two instructions need the same hardware resource in the same cycle.
3. With normal ALU forwarding, the sequence `add x5, x1, x2; sub x6, x5, x3` needs one stall.
4. Even with forwarding, an immediately following use of a loaded value normally needs one stall.
5. Static branch prediction uses the run-time history of each branch instruction.
6. In static multiple issue, the compiler decides which instructions are placed in the same issue packet.
7. A write-through cache updates both the cache and the next lower memory level on a write hit.
8. A TLB miss always means a page fault.
9. Strong scaling fixes the problem size and increases the number of processors.
10. Coarse-grain multithreading switches threads every cycle.

## Part II. Fill in the Blanks (30 points, 2 points each)

1. The three classes of pipeline hazards are ________, ________, and ________ hazards.
2. In a five-stage RISC-V pipeline with forwarding, the classic load-use stall is detected when the instruction in ID/EX is a ________ and its destination register matches a source register of the instruction in ________.
3. In a direct-mapped cache, `cache index = block address mod ________`.
4. Under predict-not-taken, a branch predicted incorrectly is a branch that is actually ________.
5. Static multiple issue is commonly associated with ________; dynamic multiple issue is commonly associated with ________.
6. In the simplified static dual-issue RISC-V model, one issue packet has one ________ slot and one ________ slot.
7. Register renaming can remove ________ and ________ dependences, but not true data dependences.
8. `AMAT = ________ + miss rate * ________`.
9. The three sources of cache misses are compulsory misses, ________ misses, and ________ misses.
10. A write-back cache needs a ________ bit to know whether a block must be written back on replacement.
11. For a 32-bit virtual address and 4KB pages, the page offset has ________ bits and the virtual page number has ________ bits.
12. A TLB is a small cache of recent ________.
13. Virtual memory normally uses the ________ write policy because disk write penalties are extremely large.
14. For Hamming SEC, the number of parity bits `r` for `m` data bits must satisfy ________.
15. Amdahl's Law for parallel fraction `F` and `N` processors is `Speedup = ________`.

## Part III. Short Problems (30 points, 5 points each)

### 1. Pipeline stalls

Consider the following RISC-V instruction sequence on a five-stage pipeline with full forwarding. Assume a load-use hazard still needs one stall.

```asm
I1: add x5, x1, x2
I2: sub x6, x5, x3
I3: lw  x7, 0(x6)
I4: and x8, x7, x9
I5: sw  x8, 4(x6)
```

For each adjacent pair, state whether a stall is needed. How many total stall cycles are required?

### 2. Static dual-issue scheduling

Use the simplified static dual-issue model:

```text
Cycle: [ ALU/branch slot ] | [ load/store slot ]
```

Only one ALU/branch and one load/store instruction can be issued in one cycle. A load-use dependence needs one cycle of separation. Schedule the following code. Use `nop` where needed.

```asm
lw   x5, 0(x10)
lw   x6, 4(x10)
addi x11, x11, 1
add  x7, x5, x6
sw   x7, 8(x10)
```

Also compute the IPC of your schedule.

### 3. Direct-mapped cache address fields

A 32-bit byte-addressed machine has a direct-mapped cache with 16KB data capacity and 16-byte blocks.

1. How many offset, index, and tag bits are there?
2. For address `0x00001234`, compute the block offset, cache index, and tag.

### 4. Cache CPI

A processor has:

```text
Base CPI = 2.0
I-cache miss rate = 2%
D-cache miss rate = 4%
Load/store instructions = 35% of all instructions
Miss penalty = 80 cycles
```

Compute the effective CPI.

### 5. One-level virtual address translation

A system has 12-bit virtual addresses and 256-byte pages. The page table says virtual page 6 maps to physical page 3.

Translate virtual address `0x6C8` into a physical address.

### 6. Amdahl's Law

96% of a program can be parallelized. What is the ideal speedup using 16 processors?

## Part IV. Comprehensive Problems (30 points, 10 points each)

### 1. Pipeline and dual-issue schedule

The following loop is executed on the same static dual-issue RISC-V machine:

```asm
Loop:
  lw   t0, 0(s1)
  add  t0, t0, s2
  sw   t0, 0(s1)
  addi s1, s1, -4
  bge  s1, zero, Loop
```

1. Fill a legal schedule table using the format `[ALU/branch] | [load/store]`.
2. Explain why the store offset may need to change if `addi s1, s1, -4` is moved before the store.
3. Compute the IPC of your schedule.

### 2. Associative cache comparison

A cache has 4 total blocks and 1-byte blocks. It starts empty. The memory block access sequence is:

```text
0, 8, 0, 6, 8
```

For each organization, list hit/miss results and the total number of hits:

1. Direct mapped
2. 2-way set associative, using LRU replacement
3. Fully associative, using LRU replacement

### 3. Multilevel cache performance

A CPU has:

```text
Base CPI = 1
Clock rate = 4GHz
L1 miss rate per instruction = 3%
L2 access time = 5ns
L2 local miss rate = 20%
Main memory access time = 100ns
```

1. Compute the effective CPI with no L2 cache.
2. Compute the effective CPI with the L2 cache.
3. Compute the speedup from adding L2.

---

# Answer Key for Mock Exam A

## Part I

1. T. Pipelining overlaps instructions and improves throughput.
2. T. Example: one memory used by IF and MEM in the same cycle.
3. F. ALU-to-ALU forwarding can handle this without a stall.
4. T. Load data is ready too late for the immediately following EX stage.
5. F. That is dynamic prediction. Static prediction does not use run-time history.
6. T. The compiler builds issue packets.
7. T. Write-through writes cache and the lower level.
8. F. The page may be in memory but not in the TLB.
9. T. Strong scaling uses a fixed problem size.
10. F. Coarse-grain switching happens on long stalls, such as L2 misses.

## Part II

1. Structural, data, control.
2. Load / `IF/ID`.
3. Number of cache blocks.
4. Taken.
5. VLIW / superscalar.
6. ALU/branch / load/store.
7. WAR / WAW.
8. Hit time / miss penalty.
9. Capacity / conflict.
10. Dirty.
11. 12 / 20.
12. Page table entries or address translations.
13. Write-back.
14. `2^r >= m + r + 1`.
15. `1 / ((1 - F) + F / N)`.

## Part III

### 1. Pipeline stalls

`I1 -> I2`: no stall. `x5` can be forwarded from the ALU result.

`I2 -> I3`: no stall. `x6` can be forwarded for the load address calculation.

`I3 -> I4`: one stall. This is a load-use hazard on `x7`.

`I4 -> I5`: no extra stall if store data forwarding is available. The store writes data in MEM, so `x8` can be forwarded.

Total: 1 stall cycle.

### 2. Static dual-issue scheduling

One legal schedule is:

```text
Cycle  ALU/branch          load/store
1      nop                 lw   x5, 0(x10)
2      addi x11, x11, 1    lw   x6, 4(x10)
3      nop                 nop
4      add  x7, x5, x6     nop
5      nop                 sw   x7, 8(x10)
```

There are 5 real instructions in 5 cycles, so:

```text
IPC = 5 / 5 = 1.0
```

Cycle 3 is needed because `add` uses `x6` loaded in cycle 2.

### 3. Direct-mapped cache address fields

Block size = 16B, so offset = `log2(16) = 4` bits.

Number of cache blocks = `16KB / 16B = 1024 = 2^10`, so index = 10 bits.

Tag = `32 - 10 - 4 = 18` bits.

For `0x00001234`:

```text
offset = 0x4
block address = floor(0x1234 / 16) = 0x123
index = 0x123 mod 1024 = 0x123 = 291
tag = 0x00001234 >> 14 = 0
```

### 4. Cache CPI

```text
I-cache stalls/instruction = 1.00 * 0.02 * 80 = 1.60
D-cache stalls/instruction = 0.35 * 0.04 * 80 = 1.12
Effective CPI = 2.0 + 1.60 + 1.12 = 4.72
```

### 5. One-level virtual address translation

Page size = 256B = `2^8`, so offset = 8 bits.

```text
VA 0x6C8:
VPN = 0x6
offset = 0xC8
VPN 6 -> PPN 3
Physical address = 0x3C8
```

### 6. Amdahl's Law

```text
Speedup = 1 / ((1 - 0.96) + 0.96 / 16)
        = 1 / (0.04 + 0.06)
        = 10
```

## Part IV

### 1. Pipeline and dual-issue schedule

One legal schedule is:

```text
Cycle  ALU/branch              load/store
1      nop                     lw   t0, 0(s1)
2      addi s1, s1, -4          nop
3      add  t0, t0, s2          nop
4      bge  s1, zero, Loop      sw   t0, 4(s1)
```

The store offset changes from `0(s1)` to `4(s1)` because `s1` has already been decremented by 4. To store back to the original address, the new base `s1` must use offset `+4`.

There are 5 real instructions in 4 cycles:

```text
IPC = 5 / 4 = 1.25
```

### 2. Associative cache comparison

Direct mapped, 4 blocks:

```text
0: miss, index 0
8: miss, index 0, replaces 0
0: miss, index 0, replaces 8
6: miss, index 2
8: miss, index 0, replaces 0
Hits = 0
```

2-way set associative, 4 blocks total gives 2 sets:

```text
0: miss, set 0
8: miss, set 0, can coexist with 0
0: hit
6: miss, set 0, replaces LRU block 8
8: miss, set 0, replaces LRU block 0
Hits = 1
```

Fully associative:

```text
0: miss
8: miss
0: hit
6: miss
8: hit
Hits = 2
```

### 3. Multilevel cache performance

Clock cycle time at 4GHz:

```text
1 / 4GHz = 0.25ns
```

Main memory penalty:

```text
100ns / 0.25ns = 400 cycles
```

No L2:

```text
CPI = 1 + 0.03 * 400 = 13
```

With L2:

```text
L2 access time = 5ns / 0.25ns = 20 cycles
CPI = 1 + 0.03 * (20 + 0.20 * 400)
    = 1 + 0.03 * 100
    = 4
```

Speedup:

```text
13 / 4 = 3.25
```

---

# Mock Exam B

## Part I. True / False (20 points, 2 points each)

Write T for true and F for false.

1. If a branch prediction is wrong, wrong-path instructions may need to be flushed.
2. In a simple in-order five-stage RISC-V pipeline, WAR and WAW are usually the main data hazards.
3. A dynamically scheduled processor may execute instructions out of order but commit them in program order.
4. Loop unrolling usually reduces code size.
5. A fully associative cache has zero index bits.
6. Increasing cache block size always reduces miss penalty.
7. Write allocate means that on a write miss, the block is brought into the cache before the write is completed.
8. Virtual memory normally uses fully associative placement and write-back.
9. `TLB hit, page table miss, cache hit` is a possible event combination.
10. Fine-grain multithreading switches threads only on long stalls such as L2 cache misses.

## Part II. Fill in the Blanks (30 points, 2 points each)

1. The ideal CPI of a single-issue pipeline after it is full is approximately ________.
2. A pipeline ________ is an inserted no-op operation that moves through the pipeline.
3. Dynamic branch prediction is based on ________ history recorded by hardware.
4. Dynamic multiple issue is also called ________ execution.
5. A group of instructions issued in the same cycle by a static multiple-issue machine is called an ________.
6. Register renaming cannot remove ________ dependences because they represent real data flow.
7. In an n-way set associative cache, `set index = block address mod ________`.
8. As associativity increases for a fixed-size cache, index bits usually ________ and tag bits usually ________.
9. LRU stands for ________.
10. A virtual-memory miss is called a ________.
11. A PTE commonly contains a valid bit, a physical page number, protection bits, a reference bit, and a ________ bit.
12. During virtual-to-physical address translation, the page ________ remains unchanged.
13. A dirty virtual-memory page must be written back to ________ before being replaced.
14. ________ scaling increases the problem size as the number of processors increases.
15. SMT stands for ________ multithreading.

## Part III. Short Problems (30 points, 5 points each)

### 1. Pipeline stall judgment

Consider:

```asm
I1: lw  x3, 0(x4)
I2: add x5, x6, x7
I3: sub x8, x3, x9
I4: or  x10, x8, x11
```

Assume a five-stage pipeline with full forwarding, and one independent instruction is enough to separate a load from its use. How many stalls are needed? Explain.

### 2. Static vs dynamic branch prediction

Classify each predictor as static or dynamic:

1. Always predict not taken.
2. Predict backward branches as taken and forward branches as not taken.
3. Use a 1-bit table recording whether the branch was taken last time.
4. Use a 2-bit saturating counter for each branch.

### 3. 4-way set associative address mapping

A 32-bit byte-addressed machine has a 4KB cache, 1-word blocks, and 4-way set associativity. One word is 4 bytes.

For address `0x140E`, compute:

1. Offset bits, index bits, and tag bits.
2. Block offset, set index, and tag value.

### 4. AMAT with two cache levels

For one memory access:

```text
L1 hit time = 1 cycle
L1 miss rate = 5%
L2 access time = 12 cycles
L2 local miss rate = 20%
Main memory penalty after an L2 miss = 100 cycles
```

Compute AMAT in cycles.

### 5. Hamming code and SEC/DED

1. How many parity bits are needed for Hamming SEC with 32 data bits?
2. In SEC/DED decoding, what does `C != 0, H = 0` mean?

### 6. Parallel speedup

A task has 10 serial additions and 900 perfectly parallel additions. Each addition takes `t`.

1. What is the speedup using 30 processors?
2. What is the maximum possible speedup as the processor count approaches infinity?

## Part IV. Comprehensive Problems (30 points, 10 points each)

### 1. Static dual-issue schedule with two unrolled iterations

The original loop is:

```asm
Loop:
  lw   x5, 0(x10)
  add  x5, x5, x11
  sw   x5, 0(x10)
  addi x10, x10, -4
  bge  x10, x0, Loop
```

The loop is unrolled twice. Use `x5` for the first loaded element and `x6` for the second. A correct unrolled body before scheduling is:

```asm
lw   x5, 0(x10)
add  x5, x5, x11
sw   x5, 0(x10)
lw   x6, -4(x10)
add  x6, x6, x11
sw   x6, -4(x10)
addi x10, x10, -8
bge  x10, x0, Loop
```

Schedule it on the static dual-issue machine:

```text
Cycle: [ ALU/branch slot ] | [ load/store slot ]
```

Assume a load-use dependence needs one cycle of separation. Compute the IPC.

### 2. Virtual memory, TLB, and cache

A system has 16-bit virtual addresses and 4KB pages. The following page table entries are known:

```text
VPN 0x0 -> PPN 0xA, valid
VPN 0x1 -> invalid
VPN 0x2 -> PPN 0x3, valid
VPN 0xF -> PPN 0x7, valid
```

1. Translate virtual address `0x2ABC`.
2. What happens for virtual address `0x1ABC`?
3. For a physically addressed cache, judge whether each combination is possible:
   - TLB miss, page table hit, cache hit
   - TLB miss, page table miss, cache hit
   - TLB hit, page table miss, cache miss

### 3. Parallel architecture concepts

Answer briefly.

1. Distinguish coarse-grain multithreading, fine-grain multithreading, and SMT.
2. Compare shared-memory multiprocessors and loosely coupled clusters.
3. Why does a GPU prefer high memory bandwidth and many threads rather than relying mainly on large low-latency caches?

---

# Answer Key for Mock Exam B

## Part I

1. T. Wrong-path work must be removed.
2. F. RAW is the main hazard in the simple in-order five-stage pipeline.
3. T. Out-of-order execution with in-order commit preserves precise state.
4. F. Loop unrolling usually increases code size.
5. T. Fully associative means there is one set, so no index bits.
6. F. Larger blocks normally increase miss penalty.
7. T. That is the definition of write allocate.
8. T. Page faults are expensive, so VM uses flexible placement and write-back.
9. F. If the page table says the page is not in memory, the TLB and cache should not have a valid copy.
10. F. That describes coarse-grain multithreading.

## Part II

1. 1.
2. Bubble.
3. Run-time branch behavior.
4. Superscalar.
5. Issue packet.
6. RAW.
7. Number of sets.
8. Decrease / increase.
9. Least Recently Used.
10. Page fault.
11. Dirty.
12. Offset.
13. Disk or swap space.
14. Weak.
15. Simultaneous.

## Part III

### 1. Pipeline stall judgment

No stalls are needed.

`I1 -> I3` is a load-use dependence on `x3`, but `I2` is independent and separates the load from its use by one cycle. `I3 -> I4` is an ALU-to-ALU RAW dependence on `x8`, handled by forwarding.

### 2. Static vs dynamic branch prediction

```text
1. Static
2. Static
3. Dynamic
4. Dynamic
```

The first two do not use run-time per-branch history. The last two use hardware state updated by previous executions.

### 3. 4-way set associative address mapping

Cache size = 4KB = 4096B. Block size = 1 word = 4B.

```text
Number of blocks = 4096 / 4 = 1024
Number of sets = 1024 / 4 = 256
offset bits = log2(4) = 2
index bits = log2(256) = 8
tag bits = 32 - 8 - 2 = 22
```

For `0x140E`:

```text
block offset = 0x140E mod 4 = 2
block address = floor(0x140E / 4) = 0x503 = 1283
set index = 1283 mod 256 = 3
tag value = 0x140E >> (8 + 2) = 0x5
```

### 4. AMAT with two cache levels

```text
AMAT = L1 hit time + L1 miss rate * (L2 access time + L2 local miss rate * memory penalty)
     = 1 + 0.05 * (12 + 0.20 * 100)
     = 1 + 0.05 * 32
     = 2.6 cycles
```

### 5. Hamming code and SEC/DED

For 32 data bits:

```text
2^r >= 32 + r + 1
r = 6, because 2^5 = 32 < 38, and 2^6 = 64 >= 39
```

In SEC/DED, `C != 0, H = 0` indicates a double-bit error: detected but not correctable.

### 6. Parallel speedup

Single-processor time:

```text
T1 = (10 + 900)t = 910t
```

With 30 processors:

```text
T30 = 10t + 900t / 30 = 40t
Speedup = 910 / 40 = 22.75
```

With infinitely many processors:

```text
Tinf = 10t
Maximum speedup = 910 / 10 = 91
```

## Part IV

### 1. Static dual-issue schedule with two unrolled iterations

One legal schedule is:

```text
Cycle  ALU/branch             load/store
1      nop                    lw   x5, 0(x10)
2      nop                    lw   x6, -4(x10)
3      add  x5, x5, x11       nop
4      add  x6, x6, x11       sw   x5, 0(x10)
5      nop                    sw   x6, -4(x10)
6      addi x10, x10, -8      nop
7      bge  x10, x0, Loop     nop
```

This keeps one cycle between each load and its use. It also preserves the original store addresses because `addi x10, x10, -8` is issued after both stores.

There are 8 real instructions in 7 cycles:

```text
IPC = 8 / 7 = 1.14
```

Other legal schedules can receive full credit if they preserve dependences, use the correct slot types, and correctly account for load-use separation.

### 2. Virtual memory, TLB, and cache

Page size = 4KB = `0x1000`, so the page offset is 12 bits.

For `0x2ABC`:

```text
VPN = 0x2
offset = 0xABC
VPN 0x2 -> PPN 0x3
Physical address = 0x3ABC
```

For `0x1ABC`:

```text
VPN = 0x1
The PTE is invalid.
This causes a page fault, so the OS must bring the page from disk/swap if the access is legal.
```

Event combinations:

```text
TLB miss, page table hit, cache hit: possible.
The translation is missing from the TLB, but the page is in memory; after translation, the data may already be in cache.

TLB miss, page table miss, cache hit: impossible for a physically addressed cache.
If the page is not in memory, its data should not be in the physical cache.

TLB hit, page table miss, cache miss: impossible.
A valid TLB entry implies the page table translation is valid logically.
```

### 3. Parallel architecture concepts

Coarse-grain multithreading switches threads only on long stalls, such as an L2 cache miss. It is simpler but cannot hide short stalls well.

Fine-grain multithreading switches threads frequently, often every cycle. It can hide both short and long stalls, but a single thread may run more slowly because cycles are shared.

SMT uses a multiple-issue dynamically scheduled processor to issue instructions from multiple threads in the same cycle when slots and functional units are available. It improves utilization of otherwise idle issue slots.

Shared-memory multiprocessors give processors one shared physical address space, so communication uses loads and stores. They are easier to program but require cache coherence and synchronization.

Loosely coupled clusters consist of independent machines with private memories and usually private operating systems, connected by a network. They scale well and are cost-effective, but communication is explicit and network bandwidth/latency can be limiting.

A GPU is designed for high-throughput data-parallel work. It uses many threads to hide memory latency and wide/high-bandwidth memory to feed many SIMD lanes. Large low-latency caches are less central because GPU workloads often stream through large data sets with massive parallelism.
