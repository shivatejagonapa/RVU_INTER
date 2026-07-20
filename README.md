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
Lead Researcher: Gonapa Shivateja 
Mobile: (+91) 7331120167

Developed as part of an intensive academic/industrial internship focused on Secure IoT in the Quantum Era.

📄 License & Usage
This repository is curated strictly for academic research, verification, and educational purposes. For technical collaborations, data verification questions, or project inquiries, please reach out directly to the lead researcher using the contact information provided above.
