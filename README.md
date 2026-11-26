# RISC-V MYTH Workshop – Processor Design Using TL-Verilog

A hands-on journey into RISC-V processor architecture, digital design and pipelining through the **RISC-V MYTH (Microprocessor for You in Thirty Hours) Workshop**.

This repository documents my learning and implementation journey, starting with the RISC-V Instruction Set Architecture (ISA) and GNU toolchain, progressing through processor microarchitecture, and culminating in the development of a pipelined RV32I processor using **TL-Verilog and Makerchip**.

---

## Project Overview

The objective of this work is to understand how a processor operates internally and how individual hardware components come together to execute instructions.

The workshop progresses through three major areas:

- **RISC-V Software Fundamentals:** Understanding the ISA, GNU compiler toolchain, assembly instructions and Application Binary Interface (ABI).
- **Digital Design and Processor Microarchitecture:** Exploring digital logic, TL-Verilog, instruction decoding, register files, ALU operations and control logic.
- **Pipelined Processor Design:** Developing a pipelined RISC-V core and understanding data hazards, register bypassing, branch handling, jumps and memory operations.

## Workshop Learning Journey

| Day | Topics Covered |
|---|---|
| **Day 1** | Introduction to RISC-V ISA, GNU compiler toolchain, assembly generation and Spike simulation |
| **Day 2** | Application Binary Interface (ABI), register conventions, memory allocation, stack pointer and instruction formats |
| **Day 3** | Digital logic design using TL-Verilog and Makerchip, including combinational and sequential logic |
| **Day 4** | Basic RISC-V processor microarchitecture: PC, instruction fetch, decode, register-file access, ALU, write-back and branching |
| **Day 5** | Pipelined RISC-V processor, register-file bypassing, branch and jump handling, load/store operations and memory integration |

Detailed notes, code snippets, screenshots and simulation observations are documented separately for each workshop day.

## Final Project – Pipelined RISC-V Processor

The final implementation progressively develops a basic single-stage RISC-V processor into a pipelined processor using TL-Verilog.

The design includes the following functional units:

- Program Counter and Instruction Memory
- Instruction Decoder and Immediate Generation
- Register File with Bypass Logic
- Arithmetic and Logic Unit (ALU)
- Branch and Jump Control Logic
- Data Memory
- Load and Store Operations
- Pipeline Validity and Squashing Logic

The implementation uses the Makerchip platform and the workshop-provided TL-Verilog macros.

## Test Program – Sum of 1 to 9

The processor is verified using a simple program that calculates the sum of integers from 1 to 9.

```text
1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9 = 45

Expected result = 45 = 0x2D
```

The final test program performs the following operations:

1. Initializes the required registers.
2. Calculates the sum using a loop and conditional branching.
3. Stores the result in data memory.
4. Loads the stored value into register x15.
5. Executes a self-loop using JAL.

The final simulation checks whether register x15 contains the expected result of 45.

## Tools and Technologies

| Tool / Technology | Purpose |
|---|---|
| RISC-V ISA (RV32I) | Processor instruction-set architecture |
| GNU RISC-V Toolchain | C compilation, assembly generation and disassembly |
| Spike Simulator | RISC-V instruction simulation and debugging |
| TL-Verilog | Processor hardware description |
| Makerchip IDE | Design, simulation and processor visualization |
| Verilog Concepts | Understanding digital hardware implementation |

## Repository Contents

This repository is organized to maintain the workshop documentation, implementation details, screenshots and observations in one place.

- Day-wise workshop documentation
- TL-Verilog implementation and code snippets
- Makerchip simulation and waveform screenshots
- Debugging observations and corrections
- Final pipelined processor implementation

## References and Learning Resources

- [RISC-V MYTH Workshop – Official Workshop Repository](https://github.com/stevehoover/RISC-V_MYTH_Workshop)
- [TL-Verilog](https://tl-x.org/)
- [Makerchip IDE](https://makerchip.com/)
- [RISC-V International](https://riscv.org/)

## Acknowledgement

This work is based on the RISC-V MYTH Workshop conducted by **VLSI System Design (VSD)** and **Redwood EDA**.

I would like to acknowledge the workshop instructors and contributors for providing the learning material, design environment and practical exercises that helped build an understanding of RISC-V processor architecture and pipelined hardware design.

---

*This repository serves as a technical record of my learning, implementation, debugging and verification throughout the RISC-V MYTH Workshop.*
