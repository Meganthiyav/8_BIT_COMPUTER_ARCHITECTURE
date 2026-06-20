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

## RTL
```verilog
module control_unit(
    input clk,
    input rst,
    input [3:0] opcode,
    input z,
    input c,

    output reg pc_inc,
    output reg pc_load,
    output reg ir_load,
    output reg mar_load,
    output reg ram_re,

    output reg a_load,
    output reg b_load,

    output reg alu_en,
    output reg flag_en,

    output reg [2:0] bus_sel
);

localparam [3:0]
    ADD=4'b0000,SUB=4'b0001,INC=4'b0010,DEC=4'b0011,
    ANDI=4'b0100,ORI=4'b0101,EXOR=4'b0110,NOT=4'b0111,
    LDA=4'b1000,STA=4'b1001,JMP=4'b1010,JC=4'b1011,
    JZ=4'b1100,JNZ=4'b1101,LDI=4'b1110,HLT=4'b1111;

localparam T0=3'd0,T1=3'd1,T2=3'd2,T3=3'd3,T4=3'd4,T5=3'd5;

reg [2:0] state, next;

always @(posedge clk or posedge rst)
begin
    if(rst) state <= T0;
    else state <= next;
end

always @(*)
begin
    case(state)
        T0: next = T1;
        T1: next = T2;
        T2: next = T3;
        T3: next = T4;
        T4: next = T5;
        T5: next = T0;
        default: next = T0;
    endcase
end

always @(*)
begin
    pc_inc=0; pc_load=0; ir_load=0; mar_load=0;
    ram_re=0; a_load=0; b_load=0;
    alu_en=0; flag_en=0;
    bus_sel=0;

    case(state)

    // FETCH
    T0: begin
        bus_sel=3'b000;
        mar_load=1;
    end
       
    T1: begin
        ram_re=1;
        ir_load=1;
        pc_inc=1;
    end

    // DECODE
    T2: ;

    // EXECUTE
    T3: begin
        case(opcode)

        LDA: begin bus_sel=3'b010; a_load=1; end
        STA: begin bus_sel=3'b100; end

        ADD,SUB,ANDI,ORI,EXOR: begin
            bus_sel=3'b010;
            b_load=1;
        end

        INC,DEC,NOT: begin
            bus_sel=3'b100;
            a_load=1;
        end

        LDI: begin
            bus_sel=3'b011;
            a_load=1;
        end

        JMP: pc_load=1;

        JC: if(c) pc_load=1;
        JZ: if(z) pc_load=1;
        JNZ: if(!z) pc_load=1;

        HLT: ;

        endcase
    end

    // ALU STAGE
    T4: begin
        case(opcode)

        ADD,SUB,ANDI,ORI,EXOR: begin
            alu_en=1;
            flag_en=1;
            a_load=1;
        end

        INC,DEC: begin
            alu_en=1;
            flag_en=1;
            a_load=1;
        end

        NOT: begin
            alu_en=1;
            a_load=1;
        end

        endcase
    end

    T5: ;

    endcase
end

endmodule
```
## TESTBENCH
```verilog
module control_unit_tb;

reg clk, rst;
reg [3:0] opcode;
reg z, c;

wire pc_inc, pc_load, ir_load, mar_load, ram_re;
wire a_load, b_load, alu_en, flag_en;
wire [2:0] bus_sel;

control_unit DUT(
    .clk(clk), .rst(rst), .opcode(opcode),
    .z(z), .c(c),
    .pc_inc(pc_inc), .pc_load(pc_load),
    .ir_load(ir_load), .mar_load(mar_load),
    .ram_re(ram_re),
    .a_load(a_load), .b_load(b_load),
    .alu_en(alu_en), .flag_en(flag_en),
    .bus_sel(bus_sel)
);

// Clock Generation: 10ns full period (5ns high, 5ns low)
initial begin
    clk = 0; 
    forever #5 clk = ~clk;
end

initial begin
    // Apply initial reset state
    rst = 1; opcode = 0; z = 0; c = 0;
    
    // Hold reset for 2.5 clock cycles to release safely on a falling clock edge
    #25 rst = 0;

    // Each instruction MUST have exactly 60ns of delay (6 states * 10ns)
    opcode = 4'b0000; #60; // ADD
    opcode = 4'b0001; #60; // SUB
    opcode = 4'b1000; #60; // LDA
    opcode = 4'b1010; #60; // JMP
    opcode = 4'b1100; z = 1; #60; // JZ

    $stop;
end

endmodule
```
## waveform
<img width="1883" height="900" alt="image" src="https://github.com/user-attachments/assets/1f584409-a009-437c-9d91-8e89bcfa3e9a" />

## schematic
<img width="1908" height="931" alt="image" src="https://github.com/user-attachments/assets/e6aa3edf-ea00-42da-b0f8-cc193eda0857" />


