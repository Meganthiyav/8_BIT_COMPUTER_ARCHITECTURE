# 🎛️ Control Unit (CU) – 8-Bit Custom Processor

## 📌 Description

The **Control Unit (CU)** is the supervisory component of the 8-bit custom processor architecture. It is responsible for coordinating and controlling all processor operations by generating the required control signals for data movement, memory access, instruction execution, and register operations.

The Control Unit interprets the opcode stored in the Instruction Register (IR) and produces a sequence of timing and control signals that direct the activities of the Program Counter (PC), Memory Address Register (MAR), Random Access Memory (RAM), Instruction Register (IR), Accumulator (A Register), B Register, Output Register, and Arithmetic Logic Unit (ALU).

The CU operates as a **Finite State Machine (FSM)** driven by the system clock. It progresses through a series of timing states (T0, T1, T2, etc.) to execute each instruction according to the processor's fetch-decode-execute cycle.

---

## ⚙️ Design Specifications

* Synchronous FSM-based architecture
* Clock-driven operation
* Instruction decoding capability
* Timing state generation (T0–T5)
* Control signal generation for datapath components
* Supports fetch, decode, and execute cycles
* Compatible with 4-bit opcode instructions
* Centralized processor control mechanism
* Reset support for state initialization

---

## 🏗️ Internal Architecture

### 🔹 Input Signals

* `clk` : System clock
* `rst` : Reset signal
* `opcode [3:0]` : Instruction opcode from Instruction Register
* `flags` : Status information from ALU (if implemented)

### 🔹 Internal Logic

* State Register
* Next-State Logic
* Instruction Decoder
* Control Signal Generator
* Timing Sequence Controller

### 🔹 Output Signals

#### Register Control Signals

* `pc_inc`
* `pc_out`
* `mar_load`
* `ir_load`
* `a_load`
* `a_out`
* `b_load`
* `out_load`

#### Memory Control Signals

* `mem_read`
* `mem_write`

#### ALU Control Signals

* `alu_enable`
* `alu_op [2:0]`

#### Bus Control Signals

* Bus enable signals for data transfer between modules

---

## ⚡ Operation Methodology

The Control Unit continuously monitors the current instruction and timing state. Based on the decoded opcode and present state, it generates the appropriate control signals required to execute the instruction.

The instruction execution process is divided into three major phases:

### Instruction Fetch

The next instruction address is transferred from the Program Counter to memory, and the instruction is loaded into the Instruction Register.

### Instruction Decode

The opcode field of the instruction is decoded to determine the required processor operation.

### Instruction Execute

Control signals corresponding to the decoded instruction are generated to perform arithmetic, logical, memory, or output operations.

---

## 🔄 Execution Flow

```text
Clock Signal
      ↓
State Transition
      ↓
Instruction Decode
      ↓
Control Signal Generation
      ↓
Datapath Operation
      ↓
Next State
```

At each clock edge, the Control Unit advances to the next timing state and activates the control signals required for that phase of execution.

---

## ⏱️ Timing State Control

### T0 – Address Transfer

```text
PC → Bus
Bus → MAR
```

Control Signals:

```text
PC_OUT = 1
MAR_LOAD = 1
```

---

### T1 – Instruction Fetch

```text
RAM → Bus
Bus → IR
PC + 1
```

Control Signals:

```text
MEM_READ = 1
IR_LOAD = 1
PC_INC = 1
```

---

### T2 – Instruction Decode

```text
IR Opcode Decoded
```

Control Signals:

```text
Instruction Decoder Active
```

---

### T3 – T5 – Instruction Execution

Execution control signals depend on the decoded opcode.

Examples:

```text
LDA  → Load Accumulator
ADD  → ALU Addition
SUB  → ALU Subtraction
OUT  → Output Register Load
HLT  → Processor Halt
```

The Control Unit generates the corresponding sequence of control signals required for each instruction.

---

## 🎯 Role in the Processor

The Control Unit serves as the **central coordinator** of the processor and determines the sequence of operations required for instruction execution. It synchronizes all datapath components, controls data movement across the bus, initiates memory accesses, manages ALU operations, and ensures proper execution of every instruction.

By generating precise timing and control signals, the Control Unit enables seamless interaction between the processor's registers, memory subsystem, and arithmetic logic circuitry, making it the core decision-making component of the processor architecture.

