# Benchmarking Post-Quantum Cryptography (PQC) on Resource-Constrained Platforms

This repository documents the comprehensive research, literature review, and experimental benchmarking of NIST FIPS-compliant Post-Quantum Cryptography (PQC) algorithms targeted for next-generation microcontrollers, specifically the Raspberry Pi RP2350.

---

## 📌 Project Overview
As quantum computing technology matures, traditional public-key cryptography (such as RSA and ECC) becomes susceptible to Shor's algorithm. This project evaluates the real-world performance, latency, memory utilization, and hardware-specific design constraints of newly standardized, lattice-based post-quantum algorithms on edge nodes and IoT hardware infrastructure. 

The primary hardware architecture under test is the **Raspberry Pi RP2350**—a groundbreaking system-on-a-chip (SoC) that features a dual-core design capable of switching dynamically between ARM Cortex-M33 and Hazard3 RISC-V architectures on the exact same piece of silicon. This enables a true head-to-head comparison of cryptographic instruction set efficiency without variations in memory bus latency or fabrication processes.

### Key Objectives:
* **Algorithm Profiling:** Benchmarking execution times, clock cycle counts, and throughput metrics for Key Generation (KeyGen), Encapsulation/Signing, and Decapsulation/Verification primitives.
* **Architectural Comparison:** Quantifying the performance, instruction density, and efficiency gap between ARM (Cortex-M33) and RISC-V (Hazard3) instruction sets running identical lattice workloads.
* **Resource Optimization:** Profiling runtime memory footprints (static and dynamic stack/heap utilization), Flash memory allocation, and analyzing floating-point unit (FPU) dependencies across distinct mathematical schemes.
* **Hardware Acceleration Potential:** Identifying optimization bottlenecks within Number Theoretic Transforms (NTT) to explore custom assembly vectorization and hardware-software co-design.

---

## 📂 Comprehensive Repository Structure

### 🔬 Core Research & Scripts
* `pqc_test.py.txt` - Python-based benchmarking and simulation script used to map algorithmic complexity, parse raw latency vectors, and model initial PQC math baselines.
* `Research_Gap_Analysis_Hazard3_PQC.pdf` - An analytical deep dive into existing embedded PQC literature, defining what our study uniquely addresses regarding the open-source Hazard3 RISC-V pipeline implementation.
* `Multicore_Implementation_of_Kyber_on_RP2350A_for_Resource_Constrained_Environments.pdf` - A pivotal target reference study analyzing multi-threaded execution models, core synchronization overhead, and inter-processor communication for Kyber/ML-KEM on the RP2350 architecture.
* `p(2) Differents.docx` - Comparative documentation tracking implementation nuances, parameter set variations, and performance trade-offs across evaluated variants.
* `p(3) Microcontroller Info.docx` - A granular hardware profile detailing the RP2350's internal bus structures, memory mapping, peripheral channels, and instruction pipeline designs.
* `P(2)- Selected Devices & final Keys (13-06-26).docx` - Cryptographic parameters blueprint detailing the operational key structures, public key sizes, ciphertext formats, and hardware targets finalized during June 2026 project milestones.

### 📅 Weekly Progress Tracking Checkpoints
* `Weekly report 1.docx` - Focuses on initial foundational literature review, assessing the global threat landscape of secure IoT in quantum environments, establishing project objectives, and formalizing the hardware inventory layout.
* `Weekly report 2.docx` - Focuses on the structural decomposition of target primitives, setting up virtual simulation frameworks, defining environment variables, and establishing compiler toolchain setups.
* `Weekly report 3.docx` - Focuses on the implementation of local baseline profiling via custom execution scripts, compiling initial test vectors, and defining the specific ARM vs RISC-V evaluation matrices.
* `Weekly report 4.docx` - Focuses on parallel core extraction strategies, analyzing dual-core task distribution setups, profiling shared memory contention, and gathering early latency profiling metrics.
* `Weekly report 5.docx` - Focuses on the finalization of experimental latency matrices, testing compiler optimizations (`-O2`, `-O3`, `-Os`), validating architectural performance variations, and compiling comprehensive project documentation sets.
* `Weekly report 6.docx` - Detailing the migration from local simulation to hardware setting environments, flash deployment configurations, setting up hardware timing hooks via standard internal SysTick counters, and debugging low-level driver integrations on the RP2350 silicon.
* `Weekly report 7.docx` - Execution of actual hardware benchmarking pipelines, collecting raw execution cycle datasets for ML-KEM and ML-DSA across both core architectures, and resolving compiler optimizations issues during hardware cross-compilation loops.
* `Weekly report 8.docx` - Comprehensive processing of benchmarking datasets, calculating structural deviations across the ARM Cortex-M33 vs Hazard3 RISC-V ISAs, finalizing the technical paper draft tables, and building the final project visualization charts.
* 
### 📊 Presentation Decks
* `P-1 Secure Iot in Quantum Computers.pptx` - Comprehensive introductory presentation detailing mathematical threat vectors, Shor's and Grover's algorithmic impacts, post-quantum options, and edge vulnerability profiles.
* `Post-Quantum_IoT_Security.pptx` - Advanced technical defense deck focusing on the practical integration of lattice-based schemes into lightweight, energy-constrained communication protocols.

### 📖 Primary Literature & Hardware Datasheets
* `Ml-KEM Fips(203).pdf` - Official NIST FIPS 203 standard documentation outlining the definitive specification for Module-Lattice-Based Key-Encapsulation Mechanisms.
* `RP-008304-DS-2-pico-2-w-datasheet.pdf` - Official hardware datasheet for the Raspberry Pi Pico 2 / RP2350 microcontroller, providing critical insights into clock configurations, flash interfaces, and memory banks.
* `kannwischer-pqm4.pdf` & `nttm4-20190513.pdf` - Academic foundation papers detailing the industry-standard `pqm4` benchmarking framework and optimizing Number Theoretic Transforms (NTT) on Cortex-M architectures.
* `cryptography-08-00021.pdf`, `electronics-12-01901.pdf`, `jdmsc-2331.pdf`, `sensors-22-02484.pdf` - Peer-reviewed academic research papers investigating state-of-the-art PQC implementations, physical side-channel attack models, and efficiency boundaries across real-world embedded IoT devices.

---

## 📊 Evaluated PQC Algorithms

This study focuses rigorously on the standardized FIPS implementations of lattice-based cryptography, moving away from legacy parameters to adopt the finalized NIST guidelines:

1. **ML-KEM (FIPS 203):** Formally derived from Kyber. A secure key-encapsulation mechanism relying on the foundational hardness of the Module Learning-With-Errors (M-LWE) problem. Characterized by its highly optimized, symmetrical execution times across encapsulation and decapsulation, making it ideal for swift session key exchange.
2. **ML-DSA (FIPS 204):** Formally derived from Dilithium. A digital signature scheme built upon the M-LWE framework using the "Fiat-Shamir with Aborts" methodology. It offers highly balanced signature/public-key sizes and highly predictable, non-floating-point performance metrics.
3. **FN-DSA (FIPS 206 Draft):** Formally derived from Falcon. A digital signature scheme based on the Ring-LWE problem over NTRU lattices. It utilizes fast verification routines and minimal signature sizes, but presents signature-generation bottlenecks due to complex floating-point requirements (Gaussian sampling) that demand native Floating-Point Unit (FPU) support.

### Target Parameters Matrix

| Metric / Parameter | ML-KEM-512 | ML-DSA-44 | FN-DSA-512 |
| :--- | :--- | :--- | :--- |
| **Primary Cryptographic Function** | Key Encapsulation (KEM) | Digital Signature | Digital Signature |
| **Core Hardness Assumption** | Module-LWE | Module-LWE | Ring-LWE (NTRU) |
| **Security Category** | NIST Level 1 (AES-128 equivalent) | NIST Level 1 | NIST Level 1 |
| **Key Advantage** | Symmetrical speed, balanced API | Straightforward integer-only math | Exceptionally compact signatures |
| **Hardware Bottleneck** | NTT Polynomial Multiplication | Large public key transmission overhead | Strict dependency on FPU math pipelines |

---

## ⚙️ Benchmarking & Methodology

The project isolates execution performance using cycles, instruction counts, and latency measurements directly on the target hardware:

             [Algorithm Execution Workload]
                            |
                            v
                [Hardware Target (RP2350)]
                            |
    +-----------------------+-----------------------+
    |                                               |
    v                                               v
[Core 0: ARM Cortex-M33]                       [Core 1: Hazard3 RISC-V]

3-stage pipeline                             - 3-stage open-source pipeline

Native Single-Precision FPU                  - Custom Extension Support

Armv8-M DSP Extensions                       - Standard Integer Profiling


1. **Baseline Simulation & Verification:** Utilizing `pqc_test.py` to evaluate algorithmic logic, handle standard test vectors, and establish target computational expectations.
2. **Architectural Isolation:** Cross-compiling the exact same C-based primitive algorithms targeting the ARM Cortex-M33 core (utilizing hardware DSP instructions) and the Hazard3 RISC-V core independently. This quantifies variations in code density, instruction cycle efficiency, and cache behaviors.
3. **Memory Footprint Extraction:** Measuring peak stack utilization via stack canary painting and tracking static allocation requirements to ensure compliance with strict edge-node constraints.
# Benchmark Terminal Logs & Hardware Experimental Observations

---

## 🖥️ Benchmark Execution Terminal Outputs

### Core 0 Output (ARM Cortex-M33 @ 150 MHz)

```text
=============================================================
         PQC BENCHMARK SUITE - RP2350 SILICON
         TARGET ARCHITECTURE : ARM Cortex-M33
         SYSTEM CPU CLOCK    : 150 MHz
         ITERATIONS PER OP   : 1000 Runs (10 Warmup)
=============================================================

-------------------------------------------------------------
 1. ML-KEM-512 (Kyber-512) Benchmark Results
-------------------------------------------------------------
+-----------------+-----------+-----------+-----------------+
| Operation       | Mean (us) | Mean (ms) | Cycles (150MHz) |
+-----------------+-----------+-----------+-----------------+
| Key Generation  | 4869      | 4         | 730,350         |
| Encapsulation   | 5573      | 5         | 835,950         |
| Decapsulation   | 6783      | 6         | 1,017,450       |
+-----------------+-----------+-----------+-----------------+
| Combined Total  | 17225     | 17        | 2,583,750       |
+-----------------+-----------+-----------+-----------------+

-------------------------------------------------------------
 2. ML-DSA-44 (Dilithium-2) Benchmark Results
-------------------------------------------------------------
+-----------------+-----------+-----------+-----------------+
| Operation       | Mean (us) | Mean (ms) | Cycles (150MHz) |
+-----------------+-----------+-----------+-----------------+
| Key Generation  | 3120      | 3         | 468,000         |
| Signature Sign  | 8450      | 8         | 1,267,500       |
| Signature Verify| 2940      | 2         | 441,000         |
+-----------------+-----------+-----------+-----------------+
| Combined Total  | 14510     | 14        | 2,176,500       |
+-----------------+-----------+-----------+-----------------+

-------------------------------------------------------------
 3. FN-DSA-512 (Falcon-512) Benchmark Results
-------------------------------------------------------------
+-----------------+-----------+-----------+-----------------+
| Operation       | Mean (us) | Mean (ms) | Cycles (150MHz) |
+-----------------+-----------+-----------+-----------------+
| Key Generation  | 12430     | 12        | 1,864,500       |
| Signature Sign  | 4150      | 4         | 622,500         |
| Signature Verify| 1100      | 1         | 165,000         |
+-----------------+-----------+-----------+-----------------+
| Combined Total  | 17680     | 17        | 2,652,000       |
+-----------------+-----------+-----------+-----------------+

[STATUS]: ARM Cortex-M33 Benchmark Complete.




### **Core 1 Output (Hazard3 RISC-V @ 150 MHz)**
=============================================================
         PQC BENCHMARK SUITE - RP2350 SILICON
         TARGET ARCHITECTURE : Hazard3 RISC-V (rv32imac)
         SYSTEM CPU CLOCK    : 150 MHz
         ITERATIONS PER OP   : 1000 Runs (10 Warmup)
=============================================================

-------------------------------------------------------------
 1. ML-KEM-512 (Kyber-512) Benchmark Results
-------------------------------------------------------------
+-----------------+-----------+-----------+-----------------+
| Operation       | Mean (us) | Mean (ms) | Cycles (mcycle) |
+-----------------+-----------+-----------+-----------------+
| Key Generation  | 5420      | 5         | 813,000         |
| Encapsulation   | 6210      | 6         | 931,500         |
| Decapsulation   | 7530      | 7         | 1,129,500       |
+-----------------+-----------+-----------+-----------------+
| Combined Total  | 19160     | 19        | 2,874,000       |
+-----------------+-----------+-----------+-----------------+

-------------------------------------------------------------
 2. ML-DSA-44 (Dilithium-2) Benchmark Results
-------------------------------------------------------------
+-----------------+-----------+-----------+-----------------+
| Operation       | Mean (us) | Mean (ms) | Cycles (mcycle) |
+-----------------+-----------+-----------+-----------------+
| Key Generation  | 3480      | 3         | 522,000         |
| Signature Sign  | 9420      | 9         | 1,413,000       |
| Signature Verify| 3280      | 3         | 492,000         |
+-----------------+-----------+-----------+-----------------+
| Combined Total  | 16180     | 16        | 2,427,000       |
+-----------------+-----------+-----------+-----------------+

-------------------------------------------------------------
 3. FN-DSA-512 (Falcon-512) Benchmark Results
-------------------------------------------------------------
+-----------------+-----------+-----------+-----------------+
| Operation       | Mean (us) | Mean (ms) | Cycles (mcycle) |
+-----------------+-----------+-----------+-----------------+
| Key Generation  | 13850     | 13        | 2,077,500       |
| Signature Sign  | 4620      | 4         | 693,000         |
| Signature Verify| 1230      | 1         | 184,500         |
+-----------------+-----------+-----------+-----------------+
| Combined Total  | 19700     | 19        | 2,955,000       |
+-----------------+-----------+-----------+-----------------+

[STATUS]: Hazard3 RISC-V Benchmark Complete.


### **🔍 Hardware Deployment & Microarchitectural Observations**

#### **🛠️ Hardware & Toolchain Setup**
Target Hardware: Bare-metal C deployment on Raspberry Pi Pico 2 W featuring the RP2350 microcontroller configured to run at a fixed clock speed of 150 MHz.

Build System & Toolchain: Native Windows build framework using the Pico C/C++ SDK (v2.x) with CMake, Ninja, arm-none-eabi-gcc (ARMv8-M target), and riscv32-unknown-elf-gcc (RV32IMAC target).

#### **⚡ Dual-Core Execution Model**
Core Selection: RP2350's hardware architecture was leveraged to execute identical C implementation sources across both processing cores:

Core 0: ARM Cortex-M33 featuring ARMv8-M DSP and hardware divide extensions.

Core 1: Hazard3 RISC-V processor (rv32imac ISA profile).

Execution Mode: Software architecture executed sequentially to eliminate system bus contension and SRAM bank conflict overheads during raw compute evaluation.

#### **🔐 Cryptographic Implementation**
Benchmark target suite covers the primary NIST FIPS Post-Quantum Cryptography standard implementations:

ML-KEM-512 (FIPS 203): Key Generation, Encapsulation, and Decapsulation.

ML-DSA-44 (FIPS 204): Key Generation, Signature Generation, and Signature Verification.

FN-DSA-512 (Falcon-512): Key Generation, Signature Generation, and Signature Verification.

Libraries Integrated: Embedded implementations sourced directly from PQClean, pqm4, and fn-dsa.

Primitives & Keccak Kernels: Symmetric cryptographic operations (hash functions, matrix sampling) utilize fips202.c (SHAKE128/SHAKE256 permutations) and standard entropy generation primitives via randombytes.c.

#### **📊 Comparative Architectural Performance Observations**
ARM Cortex-M33 Performance Advantage: Across all evaluated PQC algorithms, the Cortex-M33 consistently outperformed the Hazard3 RISC-V core by approximately 11% to 11.5% in cycle count efficiency.

ML-KEM-512: ARM required 2,583,750 cycles vs. RISC-V 2,874,000 cycles (~11.2% speedup).

ML-DSA-44: ARM required 2,176,500 cycles vs. RISC-V 2,427,000 cycles (~11.5% speedup).

FN-DSA-512: ARM required 2,652,000 cycles vs. RISC-V 2,955,000 cycles (~11.4% speedup).

Instruction Set Efficiency: The ARM Cortex-M33's instruction density (Thumb-2 ISA) and hardware DSP extensions provide higher execution efficiency for polynomial arithmetic, NTT (Number Theoretic Transform) coefficient reduction loops, and array indexing compared to base rv32imac instruction sequences without custom RISC-V vector/PQC extensions.

#### **⏱️ Benchmarking Methodology & Timing Precision**
Warmup Loops: Every individual benchmark run included 10 warmup iterations prior to data collection to initialize caches, pipeline conditions, and clear compiler memory access overheads.

Iteration Count: Results represent the arithmetic mean across 1,000 consecutive test runs per operation.

Cycle Tracking:

Hazard3 RISC-V: Direct low-latency hardware reading of Control and Status Registers (csrr mcycle) for 1-cycle timer accuracy.

ARM Cortex-M33: Hardware timer and cycle-counter (SysTick / hardware_timer) integration mapped at 150 MHz system clock resolution.

Peripherals Used: Hardware standard SDK drivers (pico_stdlib, hardware_clocks, hardware_timer) configured for UART/USB-CDC high-speed serial stdout printout.
---

## 🛠️ Getting Started

### Prerequisites
To execute the local python-based baseline simulation and data analysis scripts, ensure you have Python 3.x installed along with mathematical or cryptographic validation libraries:

```bash
# Rename the baseline script to a standard python extension format
mv pqc_test.py.txt pqc_test.py

# Execute the local functional performance verification script
python pqc_test.py
For true hardware deployment and architectural benchmarking on the physical microcontroller:

Download and install the official Raspberry Pi Pico SDK.

Configure your build system to use the appropriate compilers (arm-none-eabi-gcc and riscv64-unknown-elf-gcc).

Refer to the internal documentation in RP-008304-DS-2-pico-2-w-datasheet.pdf to configure CMake switches for toggling between the M33 and Hazard3 core execution loops.

🔮 Future Work
Dual-Core Parallelization: Exploring multicore pipelining models to distribute extensive polynomial matrix multiplications across both available processing cores simultaneously.

Side-Channel Analysis Resistance: Investigating cache-timing variability, power analysis vulnerabilities, and branch prediction behaviors during Falcon's complex floating-point sampler routines.

RISC-V Assembly Optimization: Developing hand-optimized assembly routines using custom RISC-V bit manipulation extensions to accelerate the Number Theoretic Transform (NTT) butterflies.

Energy Consumption Mapping: Measuring real-time micro-ampere consumption during continuous signing operations to model battery lifecycle impacts for remote IoT sensor deployments.

👥 Authors & Contact
Lead Researcher: Dr. Amrutesh Bhat(Rv University)
Co-Researcher  :Gonapa Shivateja 
Mobile: (+91) 7331120167

Developed as part of an intensive academic/industrial internship focused on Secure IoT in the Quantum Era.

📄 License & Usage
This repository is curated strictly for academic research, verification, and educational purposes. For technical collaborations, data verification questions, or project inquiries, please reach out directly to the lead researcher using the contact information provided above.


==========================================================================================================================================================================================================
Revised PQC WITH G7(UPDATING  ON 12/11/2026 )
+==================================================================================================================+
|                    1. EVOLUTION OF OUR WORK: BASELINE VS. REVISED PQC BENCHMARKING                               |
|             Progression from Level-1 Generic C Baseline to Gate-G7 Compliant ML-KEM-768 Suite                    |
+========================+============================================+============================================+
| Project Dimension      | Phase 1: Initial Baseline Suite            | Phase 2: Revised Gate-G7 Suite (Final)     |
+------------------------+--------------------------------------------+--------------------------------------------+
| Target Algorithms      | NIST Level 1 Suite:                        | NIST Level 3 Standard:                     |
|                        | ML-KEM-512, ML-DSA-44, FN-DSA-512          | ML-KEM-768 (Kyber-768)                     |
+------------------------+--------------------------------------------+--------------------------------------------+
| Crypto Libraries       | Generic C / PQClean & fn-dsa baselines     | Arm: pqm4 (Hand-tuned Assembly)            |
|                        |                                            | RISC-V: mlkem-native (C + Inline B-Ext)    |
+------------------------+--------------------------------------------+--------------------------------------------+
| RISC-V ISA Profile     | Base rv32imac (Standard Integer)           | Bit-Manipulation                           |
|                        |                                            | (rv32ima_zba_zbb_zbs_zbkb_zcmp)            |
+------------------------+--------------------------------------------+--------------------------------------------+
| Statistical Metric     | Arithmetic Mean (1,000 runs, 10 warmup)    | Strict Median (1,000 runs, 10 warmup+KATs) |
+------------------------+--------------------------------------------+--------------------------------------------+
| Memory & Size          | Execution cycles & latency only            | Full Peak Stack (Canary), 0B Heap, &       |
| Profiling              |                                            | Flash Code Size                            |
+------------------------+--------------------------------------------+--------------------------------------------+
| RNG & Validation       | Standard randombytes.c                     | Hardware RP2350 TRNG + HMAC-DRBG-SHA256    |
|                        |                                            | (0 failures)                               |
+------------------------+--------------------------------------------+--------------------------------------------+
| ARM Cortex-M33 Latency | ML-KEM-512 Total:                          | ML-KEM-768 Total:                          |
|                        | 17.23 ms (2,583,750 cycles)                | 21.10 ms (3,166,332 cycles)                |
+------------------------+--------------------------------------------+--------------------------------------------+
| Hazard3 RISC-V Latency | ML-KEM-512 Total:                          | ML-KEM-768 Total:                          |
|                        | 19.16 ms (2,874,000 cycles)                | 26.56 ms (3,983,479 cycles)                |
+==================================================================================================================+


2. Final Consolidated Gate-G7 Output Box (ML-KEM-768 @ 150 MHz)
+====================================================================================================+
|                      FINAL GATE-G7 HARDWARE BENCHMARK SUMMARY (RP2350 @ 150 MHz)                   |
|                Raspberry Pi Pico 2 W | XIP Flash Placement | 1,000 Iterations per Op              |
+========================+==================+===============+==============+============+============+
| Target Core / Library  | Operation        | Median Cycles | Time (ms)    | Peak Stack | Code Size  |
+------------------------+------------------+---------------+--------------+------------+------------+
| ARM Cortex-M33         | Keypair Gen      | 1,068,438.0   | 7.12 ms      | 5,628 B    | ~5.2 KB    |
| (pqm4 / Armv8-M DSP)   | Encapsulation    | 1,035,463.0   | 6.90 ms      | 6,692 B    | ~5.2 KB    |
|                        | Decapsulation    | 1,062,431.0   | 7.08 ms      | 6,676 B    | ~5.2 KB    |
|                        | FULL HANDSHAKE   | 3,166,332.0   | 21.10 ms     | 6,692 B    | ~5.2 KB    |
+------------------------+------------------+---------------+--------------+------------+------------+
| Hazard3 RISC-V         | Keypair Gen      | 1,224,252.0   | 8.16 ms      | 14,176 B   | ~9.8 KB    |
| (mlkem-native / B-Ext) | Encapsulation    | 1,287,246.0   | 8.58 ms      | 17,360 B   | ~9.8 KB    |
|                        | Decapsulation    | 1,471,981.0   | 9.81 ms      | 18,400 B   | ~9.8 KB    |
|                        | FULL HANDSHAKE   | 3,983,479.0   | 26.56 ms     | 18,400 B   | ~9.8 KB    |
+========================+==================+===============+==============+============+============+
| DELTA (ARM vs RISC-V)  | ARM Advantage    | -20.5% Cycles | -5.46 ms     | -63.6% RAM | -46.9% ROM |
| VERIFICATION STATUS    | KATs: 100% Pass  | TRNG Faults: 0| Heap: 0 B    | Canaries: OK (Both Cores)  |
+====================================================================================================+

3. Revised PQC Research & Implementation Architecture Tree
Revised_PQC_RP2350_Framework/
├── 1_Theoretical_&_Standards_Upgrade/
│   ├── Quantum_Threat_Model (Shor's & Grover's Impact on Edge IoT)
│   ├── Security_Level_Elevation (Upgraded from NIST Level 1 -> NIST Level 3 / ML-KEM-768)
│   └── Algorithm_Scope_Refinement
│       ├── Phase_1_Broad_Survey (ML-KEM-512, ML-DSA-44, FN-DSA-512 FPU bottleneck analysis)
│       └── Phase_2_Deep_Optimization (Dedicated FIPS 203 ML-KEM-768 Lattice KEM focus)
│
├── 2_Silicon_&_ISA_Co_Design (RP2350 @ 150 MHz, XIP Flash)/
│   ├── Core_0_ARM_Cortex_M33/
│   │   ├── Library: pqm4 (Hand-optimized Armv8-M DSP & Thumb-2 assembly)
│   │   └── Efficiency: Ultra-low stack footprint (6.69 KB) & compact Flash (.text ~5.2 KB)
│   └── Core_1_Hazard3_RISC_V/
│       ├── Library: mlkem-native (Portable C with inline assembly hooks)
│       └── ISA_Upgrade: rv32imac -> rv32ima_zba_zbb_zbs_zbkb_zca_zcb_zcmp (Bit-Manipulation)
│
├── 3_Gate_G7_Hardware_Verification_Pipeline/
│   ├── Entropy_&_Correctness/
│   │   ├── Hardware_TRNG -> HMAC-DRBG-SHA256 (0 reseeds, 0 TRNG failures)
│   │   └── Pre-Run Known Answer Tests (3 KAT rounds verified via SHA-256 hash match)
│   ├── Precision_Telemetry/
│   │   ├── 10 Warmup Rounds + 1,000 Timed Iterations (Median statistical extraction)
│   │   └── Stack Canary Painting (Zero heap allocation: arena=0, inuse=0)
│   └── Host_Automation_Tooling/
│       └── parse_bench.py (USB-CDC handshake trigger, regex cycle extraction, Excel/PDF reporting)
│
└── 4_Key_Empirical_Findings/
    ├── Compute_Latency: ARM Cortex-M33 outperforms Hazard3 RISC-V by ~20.5% (21.10 ms vs 26.56 ms)
    ├── SRAM_Footprint: ARM pqm4 uses 63.6% less peak stack than mlkem-native (6.69 KB vs 18.40 KB)
    └── Flash_Density: Thumb-2 assembly reduces cryptographic code size by 46.9% (5.2 KB vs 9.8 KB)
