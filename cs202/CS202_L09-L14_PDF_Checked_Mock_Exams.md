# CS202 Computer Organization PDF-Checked Mock Final Exams

Scope: Lecture 09 to Lecture 14, RISC-V32.

These two exams were regenerated after checking the lecture PDFs and `finalRange.pdf` by local PDF stream extraction, and cross-checking with the Markdown notes. The original MiKTeX `pdftotext` command still could not complete its first-run setup in the sandbox, but the PDF files were partially readable through a standard-library extractor and the key lecture terms, examples, and textbook-style exercise sections were visible.

Format per paper: 110 points, 120 minutes, English.

Difficulty target: medium to upper-medium.

---

# Mock Exam 1

## Part I. True / False (20 points, 2 points each)

Write T for true and F for false.

1. Pipelining mainly improves throughput, not the execution latency of one individual instruction.
2. A structural hazard can be solved by adding hardware resources or by stalling the pipeline.
3. With full ALU forwarding, `add x5, x1, x2; sub x6, x5, x3` still needs one stall.
4. A load-use hazard can normally be solved by forwarding alone with no stall.
5. Static branch prediction does not use the run-time history of a branch.
6. In static multiple issue, the compiler forms issue packets.
7. In the simplified dual-issue RISC-V model, one packet may contain two load/store instructions if they are independent.
8. Write-through updates both the cache and the next lower memory level on a write hit.
9. A TLB miss always means that the referenced page is not in main memory.
10. Strong scaling fixes the problem size while increasing the number of processors.

## Part II. Fill in the Blanks (20 points, 2 points each)

1. The three types of pipeline hazards are ________, ________, and ________.
2. In a five-stage RISC-V pipeline, the stages are IF, ID, EX, ________, and ________.
3. The most common remaining data hazard after forwarding is the ________ hazard.
4. Static multiple issue is often associated with ________, while dynamic multiple issue is also called ________.
5. Register renaming can remove ________ and ________ dependences, but not RAW dependences.
6. `AMAT = ________ + miss rate * ________`.
7. A direct-mapped cache uses `index = block address mod ________`.
8. A write-back cache needs a ________ bit.
9. A virtual-memory miss is called a ________.
10. Amdahl's Law is `Speedup = ________` for parallel fraction `F` and `N` processors.

## Part III. Short Problems (30 points, 5 points each)

### 1. Pipeline stall analysis

Assume a five-stage RISC-V pipeline with full forwarding. An immediately following use of a loaded register needs one stall. Store-data forwarding is available.

```asm
I1: lw   x5, 0(x10)
I2: add  x6, x1, x2
I3: sub  x7, x5, x6
I4: lw   x8, 4(x7)
I5: or   x9, x8, x7
I6: sw   x9, 8(x7)
```

For each adjacent pair, state whether a stall is needed. Give the total number of stall cycles.

### 2. Branch prediction CPI

A program has 18% branch instructions. A predict-not-taken pipeline has a 1-cycle penalty when the prediction is wrong. 55% of branch instructions are actually taken. The base CPI is 1.0.

Compute the effective CPI including branch misprediction stalls.

### 3. Direct-mapped cache addressing

A 32-bit byte-addressed machine has an 8KB direct-mapped cache. Each cache block contains 8 words, and one word is 4 bytes.

1. Compute offset bits, index bits, and tag bits.
2. For address `0x00002ABC`, compute block offset, cache index, and tag.

### 4. Cache CPI

A processor has:

```text
Base CPI = 1.6
I-cache miss rate = 1%
D-cache miss rate = 5%
Load/store instructions = 30% of all instructions
Miss penalty = 60 cycles
```

Compute the effective CPI.

### 5. Virtual address translation

A system has 16-bit virtual addresses and 1KB pages. The page table says VPN `0xA` maps to PPN `0x5`.

Translate virtual address `0x2B7C` into a physical address.

### 6. Amdahl's Law

90% of a program can be parallelized. What is the ideal speedup using 8 processors?

## Part IV. Comprehensive Problems (40 points, 10 points each)

### 1. Static dual-issue scheduling

Use the simplified static dual-issue model:

```text
Cycle: [ ALU/branch slot ] | [ load/store slot ]
```

Rules:

```text
Only one ALU/branch and one load/store instruction can issue per cycle.
A load-use dependence needs one cycle of separation.
```

Schedule the following loop legally:

```asm
Loop:
  lw   t0, 0(s1)
  add  t0, t0, s2
  sw   t0, 0(s1)
  addi s1, s1, -4
  bge  s1, zero, Loop
```

Explain any store offset change and compute IPC.

### 2. Associative cache comparison

A cache has 4 total blocks and 1-word blocks. It starts empty. The memory block access sequence is:

```text
0, 4, 0, 8, 4, 0
```

For each organization, list hit/miss results and total hits:

1. Direct mapped
2. 2-way set associative with LRU
3. Fully associative with LRU

### 3. Multilevel cache performance

A processor has:

```text
Base CPI = 1
Clock rate = 4GHz
L1 miss rate per instruction = 2%
L2 access time = 5ns
L2 global miss rate per instruction = 0.4%
Main memory access time = 100ns
```

1. Compute effective CPI with no L2 cache.
2. Compute effective CPI with the L2 cache.
3. Compute the L2 local miss rate.
4. Compute the speedup from adding L2.

### 4. TLB, page table, and cache

For a physically indexed and physically tagged cache, judge whether each combination is possible or impossible. Explain briefly.

```text
A. TLB hit, page table hit, cache miss
B. TLB hit, page table miss, cache hit
C. TLB miss, page table hit, cache hit
D. TLB miss, page table miss, cache miss
```

Also explain the difference between a TLB and a data cache.

---

# Answer Key for Mock Exam 1

## Part I

1. T.
2. T.
3. F. ALU-to-ALU forwarding handles this without a stall.
4. F. The loaded value is ready too late for the immediately following EX stage.
5. T.
6. T.
7. F. The simplified packet has one ALU/branch slot and one load/store slot.
8. T.
9. F. The page may be in memory but absent from the TLB.
10. T.

## Part II

1. Structural, data, control.
2. MEM, WB.
3. Load-use.
4. VLIW, superscalar.
5. WAR, WAW.
6. Hit time, miss penalty.
7. Number of cache blocks.
8. Dirty.
9. Page fault.
10. `1 / ((1 - F) + F / N)`.

## Part III

### 1. Pipeline stall analysis

```text
I1 -> I2: no stall. I2 is independent.
I2 -> I3: no stall. x6 can be forwarded.
I3 -> I4: no stall. x7 can be forwarded for address calculation.
I4 -> I5: one stall. This is an immediate load-use dependence on x8.
I5 -> I6: no stall if store-data forwarding is available.
```

Total stall cycles: `1`.

### 2. Branch prediction CPI

Predict-not-taken is wrong when the branch is actually taken.

```text
Mispredictions per instruction = 0.18 * 0.55 = 0.099
Extra CPI = 0.099 * 1 = 0.099
Effective CPI = 1.0 + 0.099 = 1.099
```

Approximately `1.10`.

### 3. Direct-mapped cache addressing

Block size:

```text
8 words * 4B = 32B
offset bits = log2(32) = 5
```

Number of cache blocks:

```text
8KB / 32B = 8192 / 32 = 256 = 2^8
index bits = 8
tag bits = 32 - 8 - 5 = 19
```

For `0x00002ABC`:

```text
block offset = 0x2ABC mod 32 = 0x1C
block address = 0x2ABC >> 5 = 0x155
cache index = 0x155 mod 256 = 0x55
tag = 0x2ABC >> 13 = 0x1
```

### 4. Cache CPI

```text
I-cache stalls/instruction = 1.00 * 0.01 * 60 = 0.60
D-cache stalls/instruction = 0.30 * 0.05 * 60 = 0.90
Effective CPI = 1.6 + 0.60 + 0.90 = 3.10
```

### 5. Virtual address translation

Page size = 1KB = `0x400`, so offset = 10 bits.

```text
VA 0x2B7C:
VPN = 0x2B7C / 0x400 = 0xA
offset = 0x37C
VPN 0xA -> PPN 0x5
Physical address = 0x5 * 0x400 + 0x37C = 0x177C
```

### 6. Amdahl's Law

```text
Speedup = 1 / ((1 - 0.90) + 0.90 / 8)
        = 1 / (0.10 + 0.1125)
        = 4.71
```

## Part IV

### 1. Static dual-issue scheduling

One legal schedule is:

```text
Cycle  ALU/branch             load/store
1      nop                    lw   t0, 0(s1)
2      addi s1, s1, -4         nop
3      add  t0, t0, s2         nop
4      bge  s1, zero, Loop     sw   t0, 4(s1)
```

The store offset changes to `4(s1)` because `s1` has already been decremented by 4. To store to the original address, the new base must use offset `+4`.

```text
IPC = 5 real instructions / 4 cycles = 1.25
```

### 2. Associative cache comparison

Direct mapped, 4 blocks:

```text
0: miss
4: miss
0: miss
8: miss
4: miss
0: miss
Hits = 0
```

All three blocks map to index 0, so they keep replacing each other.

2-way set associative, 4 total blocks means 2 sets:

```text
0: miss
4: miss
0: hit
8: miss, replaces LRU block 4
4: miss, replaces LRU block 0
0: miss, replaces LRU block 8
Hits = 1
```

Fully associative:

```text
0: miss
4: miss
0: hit
8: miss
4: hit
0: hit
Hits = 3
```

### 3. Multilevel cache performance

At 4GHz:

```text
cycle time = 0.25ns
main memory penalty = 100ns / 0.25ns = 400 cycles
L2 access time = 5ns / 0.25ns = 20 cycles
```

No L2:

```text
CPI = 1 + 0.02 * 400 = 9
```

With L2:

```text
CPI = 1 + 0.02 * 20 + 0.004 * 400
    = 1 + 0.4 + 1.6
    = 3.0
```

L2 local miss rate:

```text
0.4% / 2% = 20%
```

Speedup:

```text
9 / 3.0 = 3
```

### 4. TLB, page table, and cache

```text
A. Possible. The translation exists, but the data block may miss in cache.
B. Impossible. A valid TLB entry should not exist for a page-table miss.
C. Possible. The translation is missing from TLB but present in the page table; the data may already be cached.
D. Possible. This is the page fault path; the data is also not in cache.
```

A TLB caches address translations, such as VPN to PPN mappings. A data cache stores actual data or instructions.

---

# Mock Exam 2

## Part I. True / False (20 points, 2 points each)

Write T for true and F for false.

1. A bubble is a no-op inserted into the pipeline to preserve correctness.
2. In a five-stage in-order pipeline, RAW is usually more important than WAR and WAW.
3. Dynamic branch prediction is based on hardware-recorded run-time behavior.
4. Loop unrolling can expose more instruction-level parallelism, but it may increase code size.
5. A fully associative cache has no index bits.
6. Increasing block size always improves performance.
7. Write allocate means that a write miss brings the missed block into the cache.
8. Virtual memory normally uses write-back because page faults and disk writes are very expensive.
9. A page offset changes during virtual-to-physical address translation.
10. SMT can issue instructions from more than one thread in the same cycle.

## Part II. Fill in the Blanks (20 points, 2 points each)

1. An issue packet is a group of instructions issued in the same ________.
2. The simplified static dual-issue RISC-V machine has one ________ slot and one ________ slot.
3. A 2-bit dynamic branch predictor changes strong prediction states more slowly than a ________ predictor.
4. The three sources of cache misses are compulsory, ________, and ________ misses.
5. In an n-way set-associative cache, `set index = block address mod ________`.
6. The two common write-hit policies are ________ and ________.
7. A page table entry commonly contains valid, PPN, protection, reference, and ________ bits.
8. A TLB is a cache of recent ________.
9. Weak scaling increases the ________ as the number of processors increases.
10. SIMD means Single Instruction, ________ Data.

## Part III. Short Problems (30 points, 5 points each)

### 1. Dependence types

Identify RAW, WAR, and WAW dependences in the following sequence.

```asm
I1: add x5, x1, x2
I2: sub x6, x5, x3
I3: or  x5, x7, x8
```

Which of these dependences can register renaming remove?

### 2. Static vs dynamic prediction

Classify each predictor as static or dynamic:

```text
A. Always predict not taken
B. Predict backward branches as taken
C. Use a 1-bit table storing the last outcome
D. Use a 2-bit saturating counter
```

### 3. 4-way cache mapping

A 32-bit byte-addressed system has a 16KB, 4-way set-associative cache with 16-byte blocks.

1. Compute offset bits, index bits, and tag bits.
2. For address `0x00001A2C`, compute block offset, set index, and tag.

### 4. Two-level AMAT

For one memory access:

```text
L1 hit time = 1 cycle
L1 miss rate = 4%
L2 access time = 10 cycles
L2 local miss rate = 25%
Main memory penalty after an L2 miss = 80 cycles
```

Compute AMAT.

### 5. Hamming SEC and SEC/DED

1. How many parity bits are required for Hamming SEC with 16 data bits?
2. In SEC/DED, what does `C != 0, H = 0` mean?

### 6. Scaling calculation

A task has 10 serial additions and 390 perfectly parallel additions. Each addition takes time `t`.

1. Compute speedup with 30 processors.
2. Compute maximum possible speedup as the number of processors approaches infinity.

## Part IV. Comprehensive Problems (40 points, 10 points each)

### 1. Dual-issue scheduling with an address update

Schedule the following code on the simplified static dual-issue machine. You may move `addi` earlier only if you preserve the store address.

```asm
lw   x5, 0(x10)
lw   x6, 4(x10)
add  x7, x5, x6
sw   x7, 8(x10)
addi x10, x10, 8
bge  x10, x11, Loop
```

Use the table:

```text
Cycle: [ ALU/branch slot ] | [ load/store slot ]
```

Compute IPC and explain any changed store offset.

### 2. Write policies and write-stall CPI

A processor has base CPI = 1.2. 12% of instructions are stores. A write-through cache without a write buffer must wait 50 cycles for each store to update memory.

1. Compute the effective CPI without a write buffer.
2. Explain how a write buffer helps write-through.
3. Explain why write-back needs a dirty bit.
4. Name a common pairing for write miss policy with write-back.

### 3. Virtual memory translation and events

A machine has 20-bit virtual addresses and 1KB pages.

```text
VPN 0x12 -> PPN 0x2A, valid
VPN 0x13 -> invalid
```

1. Translate virtual address `0x048F2`.
2. What happens when accessing virtual address `0x04D10`?
3. Is `TLB miss, page table hit, cache hit` possible? Explain.
4. Is `TLB miss, page table miss, cache hit` possible for a physically addressed cache? Explain.

### 4. Parallel architecture concepts

Answer briefly:

1. Distinguish task-level parallelism from a parallel processing program.
2. Compare coarse-grain multithreading, fine-grain multithreading, and SMT.
3. Compare shared-memory multiprocessors and loosely coupled clusters.
4. Why are GPUs good for data-parallel workloads but not a full replacement for CPUs?

---

# Answer Key for Mock Exam 2

## Part I

1. T.
2. T.
3. T.
4. T.
5. T.
6. F. Larger blocks can increase miss penalty and cache pollution.
7. T.
8. T.
9. F. The offset remains unchanged.
10. T.

## Part II

1. Cycle.
2. ALU/branch, load/store.
3. 1-bit.
4. Capacity, conflict.
5. Number of sets.
6. Write-through, write-back.
7. Dirty.
8. Translations or PTEs.
9. Problem size.
10. Multiple.

## Part III

### 1. Dependence types

```text
I1 -> I2 on x5: RAW, because I2 reads x5 produced by I1.
I1 -> I3 on x5: WAW, because both write x5.
I2 -> I3 on x5: WAR, because I2 reads old x5 and I3 writes x5 later.
```

Register renaming can remove WAR and WAW because they are name dependences. It cannot remove RAW because RAW is a true data dependence.

### 2. Static vs dynamic prediction

```text
A. Static
B. Static
C. Dynamic
D. Dynamic
```

### 3. 4-way cache mapping

```text
Cache size = 16KB = 16384B
Block size = 16B
Total blocks = 16384 / 16 = 1024
4-way => sets = 1024 / 4 = 256
offset bits = 4
index bits = 8
tag bits = 32 - 8 - 4 = 20
```

For `0x00001A2C`:

```text
block offset = 0xC
block address = 0x1A2C >> 4 = 0x1A2
set index = 0x1A2 mod 256 = 0xA2
tag = 0x1A2C >> 12 = 0x1
```

### 4. Two-level AMAT

```text
AMAT = 1 + 0.04 * (10 + 0.25 * 80)
     = 1 + 0.04 * 30
     = 2.2 cycles
```

### 5. Hamming SEC and SEC/DED

For 16 data bits:

```text
2^r >= 16 + r + 1
r = 5, because 2^4 = 16 < 21 and 2^5 = 32 >= 22
```

In SEC/DED, `C != 0, H = 0` means a double-bit error is detected but cannot be corrected.

### 6. Scaling calculation

```text
T1 = (10 + 390)t = 400t
T30 = 10t + 390t / 30 = 23t
Speedup = 400 / 23 = 17.39
```

As processor count approaches infinity:

```text
Tinf = 10t
Maximum speedup = 400 / 10 = 40
```

## Part IV

### 1. Dual-issue scheduling with an address update

One legal schedule is:

```text
Cycle  ALU/branch             load/store
1      nop                    lw   x5, 0(x10)
2      nop                    lw   x6, 4(x10)
3      addi x10, x10, 8        nop
4      add  x7, x5, x6         nop
5      bge  x10, x11, Loop     sw   x7, 0(x10)
```

The original store was `sw x7, 8(x10)` using the old value of `x10`. Since `addi x10, x10, 8` is moved before the store, the new `x10` equals old `x10 + 8`, so the store offset becomes `0(x10)`.

```text
IPC = 6 real instructions / 5 cycles = 1.2
```

### 2. Write policies and write-stall CPI

Without a write buffer:

```text
Extra CPI = 0.12 * 50 = 6.0
Effective CPI = 1.2 + 6.0 = 7.2
```

A write buffer lets the CPU place the write into a buffer and continue execution while memory is updated in the background. The CPU stalls only if the buffer is full.

Write-back needs a dirty bit because a modified cache block may differ from memory. If the dirty block is replaced, it must be written back.

A common pairing is:

```text
write-back + write allocate
```

### 3. Virtual memory translation and events

Page size = 1KB = `0x400`, so offset = 10 bits.

For `0x048F2`:

```text
VPN = 0x048F2 / 0x400 = 0x12
offset = 0x0F2
VPN 0x12 -> PPN 0x2A
Physical address = 0x2A * 0x400 + 0x0F2 = 0xA8F2
```

For `0x04D10`:

```text
VPN = 0x13
VPN 0x13 is invalid, so a page fault occurs.
```

`TLB miss, page table hit, cache hit` is possible. The TLB may not contain the translation, but the page table can show the page is in memory. After translation, the data may already be in the physical cache.

`TLB miss, page table miss, cache hit` is impossible for a physically addressed cache. If the page is not in main memory, its physical cache block should not be present.

### 4. Parallel architecture concepts

Task-level parallelism runs multiple independent jobs at the same time, such as many web requests. A parallel processing program splits one program across processors to solve one problem faster.

Coarse-grain multithreading switches threads on long stalls, such as cache misses. Fine-grain multithreading switches frequently, often every cycle. SMT can issue instructions from multiple threads in the same cycle on a multiple-issue processor.

Shared-memory multiprocessors provide one shared physical address space and communicate through loads and stores, but need cache coherence and synchronization. Loosely coupled clusters use independent machines with private memories and communicate through messages or networks; they scale well but have higher communication overhead and more complex administration.

GPUs are good for data-parallel workloads because many lanes and many threads can perform the same operation on many data elements with high memory bandwidth. They are not full CPU replacements because CPUs are better for control-heavy, branch-heavy, low-latency, operating-system and general sequential workloads.

