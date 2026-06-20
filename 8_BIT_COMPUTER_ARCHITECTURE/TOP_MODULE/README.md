# 🖥️ Top Module – 8-Bit Computer Architecture

## System Integration Layer

The Top Module represents the complete integration of the 8-bit computer architecture. It serves as the highest level of the design hierarchy, interconnecting all datapath, memory, control, and peripheral modules into a fully functional processor system.

Rather than performing computations itself, the Top Module acts as the architectural framework that coordinates communication between individual hardware blocks, enabling the processor to execute instructions through the Fetch–Decode–Execute cycle.

---

## Architectural Composition

The processor is constructed by integrating the following core modules:

### Processing Units

* Arithmetic Logic Unit (ALU)
* Flag Register
* A Register
* B Register
* Output Register

### Instruction Handling

* Program Counter (PC)
* Instruction Register (IR)
* Control Unit (FSM)

### Memory Subsystem

* Memory Address Register (MAR)
* Dual-Port RAM (16 × 8)

### Communication Infrastructure

* Internal Bus System

### User Interface

* Seven Segment Decoder
* Clock Divider

---

## System-Level Data Flow

The processor operates using a shared bus architecture where data is transferred between modules under the supervision of the Control Unit.

```text id="bz0y08"
             ┌─────────────┐
             │ Program     │
             │ Counter     │
             └──────┬──────┘
                    │
                    ▼
             ┌─────────────┐
             │     MAR     │
             └──────┬──────┘
                    │
                    ▼
             ┌─────────────┐
             │    RAM      │
             └──────┬──────┘
                    │
                    ▼
             ┌─────────────┐
             │     IR      │
             └──────┬──────┘
                    │
                    ▼
             ┌─────────────┐
             │ Control Unit│
             └──────┬──────┘
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
     Registers              ALU
          │                   │
          └─────────┬─────────┘
                    ▼
             Output Register
```

---

## Instruction Execution Framework

The integrated system follows a Fetch–Decode–Execute architecture.

### Fetch Stage

```text id="s3mlhh"
PC → MAR → RAM → IR
```

The next instruction is retrieved from memory and loaded into the Instruction Register.

### Decode Stage

```text id="3hlv8e"
IR → Control Unit
```

The Control Unit interprets the opcode and generates the required control signals.

### Execute Stage

```text id="51k5yw"
Registers → ALU → Flags → Output
```

The requested operation is performed and the result is stored or displayed.

---

## Design Characteristics

### Modular Architecture

Each subsystem is implemented as an independent Verilog module, enabling easier verification, debugging, and future expansion.

### Shared Bus Organization

A centralized bus minimizes interconnect complexity while supporting efficient communication between components.

### FSM-Based Control

The Control Unit sequences instruction execution through well-defined processor states.

### Fully Synchronous Design

All sequential modules operate using a common clock domain, ensuring predictable timing behavior.

### FPGA-Friendly Implementation

The architecture is designed for straightforward synthesis and deployment on FPGA platforms.

---

## Architectural Significance

The Top Module represents the complete realization of the 8-bit processor. It transforms a collection of independent hardware modules into a cohesive computing system capable of fetching instructions, processing data, managing memory, and producing observable outputs.

As the highest abstraction level of the design, it embodies the complete processor architecture and serves as the central integration point for all hardware functionality.

## RTL
## TESTBENCH
## waveform
## schematic

