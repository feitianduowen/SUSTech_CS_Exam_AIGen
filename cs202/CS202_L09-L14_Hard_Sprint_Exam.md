# CS202 Computer Organization Hard Sprint Mock Exam

Scope: Lecture 09 to Lecture 14, based on lecture slides and RISC-V32.

Format: 110 points, 120 minutes, English.

Difficulty: harder than the normal mock exams. The paper emphasizes multi-step reasoning, schedule legality, cache/TLB interactions, and performance calculations.

---

# Hard Sprint Exam

## Part I. True / False With Correction (20 points, 2 points each)

Write T or F. If the statement is false, write a short correction.

1. A load-use hazard remains difficult even with forwarding because load data becomes available after the following instruction would need it in EX.
2. In a five-stage in-order RISC-V pipeline, forwarding can remove all data hazards without any stall.
3. A static branch predictor may predict backward branches as taken without recording the run-time history of that branch.
4. In static dual issue, two ALU instructions can be issued in the same packet if they are independent.
5. Dynamic scheduling can allow later independent instructions to execute before an earlier stalled instruction.
6. Register renaming can eliminate RAW, WAR, and WAW dependences.
7. Increasing associativity usually reduces conflict misses but can increase hit time and hardware complexity.
8. In virtual memory, write-through is usually preferred because it keeps disk and memory always consistent.
9. A TLB miss followed by a page-table hit may still end with a cache hit.
10. Strong scaling is usually more limited by Amdahl's Law than weak scaling.

## Part II. Fill in the Blanks (20 points, 2 points each)

1. The three pipeline hazards are ________, ________, and ________.
2. In the simplified dual-issue RISC-V model, the two slots are ________ and ________.
3. Dynamic multiple issue is also called ________, while static multiple issue is often associated with ________.
4. The formula for AMAT is ________.
5. In a set-associative cache, the number of sets equals total cache blocks divided by ________.
6. In virtual address translation, the ________ changes from virtual to physical, while the ________ remains unchanged.
7. A page fault is handled by the ________, not only by ordinary combinational hardware.
8. Hamming SEC requires `r` parity bits satisfying ________.
9. ________ multithreading switches only on long stalls; ________ multithreading can issue instructions from multiple threads in the same cycle.
10. `Speedup = 1 / ((1 - F) + F / N)` is known as ________.

## Part III. Short Problems (30 points, 5 points each)

### 1. Hazard and stall analysis

Assume a five-stage RISC-V pipeline with full ALU forwarding, load-use hazard detection, and store-data forwarding. A load-use dependence needs exactly one stall when the consumer immediately follows the load.

```asm
I1: lw   x5, 0(x10)
I2: add  x6, x1, x2
I3: sub  x7, x5, x6
I4: lw   x8, 4(x7)
I5: or   x9, x8, x7
I6: sw   x9, 8(x7)
```

For each adjacent pair, state whether a stall is needed and give the total number of stall cycles.

### 2. Branch prediction penalty

A program has 20% branch instructions. The pipeline predicts not taken. Assume:

```text
60% of branches are actually taken
Misprediction penalty = 1 cycle
Base CPI without branch stalls = 1.0
```

Compute the effective CPI due to branch mispredictions.

### 3. Cache address fields and metadata

A 32-bit byte-addressed machine has a 32KB, 4-way set-associative cache. The block size is 32 bytes. Each block has one valid bit and one dirty bit.

1. Compute offset bits, index bits, and tag bits.
2. Compute the total metadata bits for valid, dirty, and tag storage.

### 4. Cache miss classification

A direct-mapped cache has 4 blocks, 1 word per block. It starts empty. The access sequence is:

```text
0, 4, 8, 0, 4, 8
```

Assume the numbers are memory block addresses.

1. List hit/miss results.
2. Classify the misses as compulsory or conflict when possible.

### 5. Virtual memory translation and page table size

A machine has:

```text
32-bit virtual addresses
4KB pages
4-byte PTEs
```

1. How many VPN bits and page-offset bits are there?
2. What is the size of a single-level page table per process?
3. If virtual address `0x00403ABC` has VPN `0x403` and the page table maps VPN `0x403` to PPN `0x1F2`, what is the physical address?

### 6. Amdahl plus load imbalance

A computation has 5% serial work and 95% parallel work. It runs on 20 processors.

1. What is the ideal Amdahl speedup assuming perfect balance?
2. Suppose the parallel work is imbalanced so that one processor takes 10% of the parallel work while the other 19 processors evenly share the remaining 85%. Use normalized single-processor work = 1. Compute the new speedup.

## Part IV. Comprehensive Problems (40 points, 10 points each)

### 1. Static dual-issue scheduling

Use the simplified static dual-issue RISC-V model:

```text
Cycle: [ ALU/branch slot ] | [ load/store slot ]
```

Rules:

```text
Only one ALU/branch instruction and one load/store instruction can issue per cycle.
A load-use dependence needs one cycle of separation.
The load/store instruction in a packet cannot use an ALU result produced by the ALU instruction in the same packet.
```

Schedule the following code with as few cycles as possible. You may reorder instructions only if semantics are preserved.

```asm
lw   x5, 0(x10)
lw   x6, 4(x10)
add  x7, x5, x6
addi x10, x10, 8
sw   x7, 0(x11)
add  x12, x10, x13
lw   x14, 0(x12)
and  x15, x14, x7
```

Compute the IPC of your schedule.

### 2. Multilevel cache CPI and local/global miss rates

A processor has:

```text
Base CPI = 1.2
Clock rate = 5GHz
L1 I-cache miss rate = 1.5%
L1 D-cache miss rate = 3%
Load/store instructions = 40% of all instructions
L2 access time = 6ns
Main memory access time = 90ns
L2 local miss rate = 25% for both I-side and D-side L1 misses
```

Assume L1 hit time is included in base CPI.

1. Convert L2 access time and main memory access time into CPU cycles.
2. Compute I-side memory stall cycles per instruction.
3. Compute D-side memory stall cycles per instruction.
4. Compute the effective CPI.

### 3. TLB, page table, and cache event reasoning

A machine uses physically indexed and physically tagged caches. Judge each event combination as possible or impossible. For each impossible case, explain why.

```text
A. TLB hit, page table hit, cache miss
B. TLB hit, page table miss, cache hit
C. TLB miss, page table hit, cache hit
D. TLB miss, page table hit, cache miss
E. TLB miss, page table miss, cache hit
F. TLB miss, page table miss, cache miss
```

Also answer: why is a TLB not the same thing as a cache for data?

### 4. Parallel architecture synthesis

Answer all parts.

1. A program computes `Y[i] = a * X[i] + Y[i]` for a very large array. Which is more suitable: scalar RISC-V code, SIMD/vector execution, or a loosely coupled cluster? Explain.
2. A web service handles millions of mostly independent user requests. Which is more suitable: SIMD/vector execution, shared-memory multicore, or a loosely coupled cluster? Explain.
3. Distinguish coarse-grain multithreading, fine-grain multithreading, and SMT in terms of when they switch or issue threads.
4. Explain why increasing the number of processors cannot guarantee proportional speedup.

---

# Answer Key and Explanations

## Part I

1. T.
2. F. ALU forwarding cannot remove the immediate load-use stall.
3. T.
4. F. In the simplified model, there is only one ALU/branch slot per packet.
5. T.
6. F. Register renaming removes WAR and WAW name dependences, but not RAW true dependences.
7. T.
8. F. Virtual memory uses write-back because disk write penalties are extremely large.
9. T. The translation may be absent from TLB but present in the page table, and the physical block may already be cached.
10. T.

## Part II

1. Structural, data, control.
2. ALU/branch; load/store.
3. Superscalar; VLIW.
4. `Hit time + miss rate * miss penalty`.
5. Associativity, or number of ways.
6. Page number; page offset.
7. Operating system.
8. `2^r >= m + r + 1`.
9. Coarse-grain; simultaneous.
10. Amdahl's Law.

## Part III

### 1. Hazard and stall analysis

```text
I1 -> I2: no stall. I2 is independent.
I2 -> I3: no stall. x6 is produced by an ALU instruction and can be forwarded.
I3 -> I4: no stall. x7 is used as the load address and can be forwarded to EX.
I4 -> I5: one stall. This is an immediate load-use dependence on x8.
I5 -> I6: no stall if store-data forwarding is available.
```

Total stall cycles: `1`.

### 2. Branch prediction penalty

Predict-not-taken is wrong when the branch is actually taken.

```text
Mispredictions per instruction = 0.20 * 0.60 = 0.12
Extra CPI = 0.12 * 1 = 0.12
Effective CPI = 1.0 + 0.12 = 1.12
```

### 3. Cache address fields and metadata

```text
Cache data capacity = 32KB = 32768B
Block size = 32B
Total blocks = 32768 / 32 = 1024
4-way => sets = 1024 / 4 = 256
offset bits = log2(32) = 5
index bits = log2(256) = 8
tag bits = 32 - 8 - 5 = 19
```

Metadata per block:

```text
valid + dirty + tag = 1 + 1 + 19 = 21 bits
Total metadata = 1024 * 21 = 21504 bits
```

### 4. Cache miss classification

Direct mapped with 4 cache blocks:

```text
block 0 -> index 0: miss, compulsory
block 4 -> index 0: miss, compulsory, replaces 0
block 8 -> index 0: miss, compulsory, replaces 4
block 0 -> index 0: miss, conflict
block 4 -> index 0: miss, conflict
block 8 -> index 0: miss, conflict
```

Hit count is 0. The first access to each block is compulsory; later misses happen because all three blocks conflict at index 0.

### 5. Virtual memory translation and page table size

```text
Page size = 4KB = 2^12
Page offset bits = 12
VPN bits = 32 - 12 = 20
Number of PTEs = 2^20
Page table size = 2^20 * 4B = 4MB
```

The offset of `0x00403ABC` is the low 12 bits:

```text
offset = 0xABC
VPN 0x403 -> PPN 0x1F2
Physical address = 0x1F2ABC
```

### 6. Amdahl plus load imbalance

Perfect balance:

```text
Speedup = 1 / (0.05 + 0.95 / 20)
        = 1 / (0.05 + 0.0475)
        = 10.26
```

With imbalance:

```text
Serial time = 0.05
Slowest processor parallel time = 0.10
Other processors each take 0.85 / 19 = 0.0447
Parallel phase time is determined by the slowest processor = 0.10
Total time = 0.05 + 0.10 = 0.15
Speedup = 1 / 0.15 = 6.67
```

The result is much worse because the slowest processor determines the parallel phase completion time.

## Part IV

### 1. Static dual-issue scheduling

One optimal legal schedule is:

```text
Cycle  ALU/branch             load/store
1      nop                    lw   x5, 0(x10)
2      nop                    lw   x6, 4(x10)
3      addi x10, x10, 8        nop
4      add  x7, x5, x6         nop
5      add  x12, x10, x13      sw   x7, 0(x11)
6      nop                    lw   x14, 0(x12)
7      nop                    nop
8      and  x15, x14, x7       nop
```

Why this is legal:

```text
add x7 waits long enough for both loads.
addi x10 can move before add x7 because x7 does not use x10.
sw x7, 0(x11) does not depend on the updated x10.
add x12 uses the updated x10, so it must come after addi.
lw x14 uses x12, so it cannot be in the same packet as add x12.
and x15 uses loaded x14, so one separation cycle is required after lw x14.
```

There are 8 real instructions in 8 cycles:

```text
IPC = 8 / 8 = 1.0
```

Other schedules can receive full credit if they preserve all dependences and respect slot restrictions.

### 2. Multilevel cache CPI and local/global miss rates

Clock cycle time:

```text
5GHz => 0.2ns per cycle
L2 access = 6ns / 0.2ns = 30 cycles
Memory access = 90ns / 0.2ns = 450 cycles
```

Average penalty per L1 miss:

```text
30 + 0.25 * 450 = 142.5 cycles
```

I-side stalls per instruction:

```text
1.00 * 0.015 * 142.5 = 2.1375
```

D-side stalls per instruction:

```text
0.40 * 0.03 * 142.5 = 1.71
```

Effective CPI:

```text
1.2 + 2.1375 + 1.71 = 5.0475
```

So the effective CPI is about `5.05`.

### 3. TLB, page table, and cache event reasoning

```text
A. TLB hit, page table hit, cache miss: possible.
   TLB hit means the translation is available. The data may still miss in cache.

B. TLB hit, page table miss, cache hit: impossible.
   A valid TLB entry should not exist for a page that is not present in memory.

C. TLB miss, page table hit, cache hit: possible.
   The translation is absent from TLB but present in the page table. After translation, the data may hit in cache.

D. TLB miss, page table hit, cache miss: possible.
   The page is in memory, but the requested block may not be in cache.

E. TLB miss, page table miss, cache hit: impossible.
   If the page is not in main memory, a physically addressed cache should not contain that page's data.

F. TLB miss, page table miss, cache miss: possible.
   This is the page fault case.
```

A TLB is not a cache for data. It caches address translations, usually virtual page number to physical page number mappings. A data/instruction cache stores actual program data or instructions.

### 4. Parallel architecture synthesis

1. `Y[i] = a * X[i] + Y[i]` is highly data-parallel. SIMD/vector execution is the best match among the listed choices because the same operation is applied to many independent data elements. Scalar RISC-V works but uses many repeated instructions; a loosely coupled cluster may have too much communication overhead unless the array is extremely large and distributed.

2. A web service with millions of mostly independent requests fits a loosely coupled cluster well. Requests can be distributed across many machines, improving throughput, scalability, and availability. SIMD is not suitable because different requests do not normally execute the same instruction stream on adjacent data. Shared-memory multicore helps within one server but does not scale as far as a cluster.

3. Coarse-grain multithreading switches threads on long stalls. Fine-grain multithreading switches frequently, often every cycle. SMT can issue instructions from multiple threads in the same cycle on a multiple-issue dynamically scheduled processor.

4. More processors do not guarantee proportional speedup because some parts of the program may be serial, communication and synchronization add overhead, memory bandwidth can become a bottleneck, load imbalance makes the slowest processor determine total time, and some algorithms do not expose enough parallel work.

