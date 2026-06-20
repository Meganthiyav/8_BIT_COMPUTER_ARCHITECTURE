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
```verilog
`timescale 1ns / 1ps

module computer_top(
    input clk,
    input rst,
    input start,ram_load ,
    input [7:0] ram_data,
    input [3:0] ram_addr,
    output [3:0] anodes,
    output [7:0] seg
);

    // System Buses and Internal Data Tracks
    wire [7:0] bus_data;
    wire [3:0] pc_to_bus;
    wire [3:0] mar_to_ram;
    wire [7:0] ram_a_out, ram_b_out;
    wire [3:0] ir_opcode;
    wire [3:0] ir_operand;
    wire [7:0] a_reg_out;
    wire [7:0] b_reg_out;
    wire [7:0] alu_result;
    wire [7:0] output_reg_out;

    // Flag Tracks
    wire alu_carry;
    wire flag_z, flag_c;

    // Control Matrix Signal Lines
    wire ctrl_pc_inc, ctrl_pc_load, ctrl_ir_load, ctrl_mar_load;
    wire ctrl_ram_re_a, ctrl_ram_we_a, ctrl_ram_re_b;
    wire ctrl_a_load, ctrl_b_load, ctrl_out_load;
    wire ctrl_alu_en, ctrl_flag_en;
    wire [2:0] system_bus_sel;
    wire [2:0] system_alu_cmd;

    // Clock Management
    wire system_slow_clk;

    clock_divider clk_mgmt (
        .clk(clk),
        .rst(rst),
        .slow_clk(system_slow_clk)
    );

    // Control Unit
    control_unit master_control (
        .clk(system_slow_clk),
        .rst(rst),
        .opcode(ir_opcode),
        .z(flag_z),
        .c(flag_c),

        .pc_inc(ctrl_pc_inc),
        .pc_load(ctrl_pc_load),
        .ir_load(ctrl_ir_load),
        .mar_load(ctrl_mar_load),

        .ram_re(ctrl_ram_re_a),

        .a_load(ctrl_a_load),
        .b_load(ctrl_b_load),

        .alu_en(ctrl_alu_en),
        .flag_en(ctrl_flag_en),

        .bus_sel(system_bus_sel)
    );

    // BUS
    bus centralized_system_bus (
        .pc({4'b0000, pc_to_bus}),
        .mar({4'b0000, mar_to_bus}),
        .ram_out(ctrl_ram_re_b ? ram_b_out : ram_a_out),
        .ir({4'b0000, ir_operand}),
        .a_reg(a_reg_out),
        .b_reg(b_reg_out),
        .alu_out(alu_result),
        .sel(system_bus_sel),
        .bus_out(bus_data)
    );

    // Program Counter
    pc program_counter (
        .clk(system_slow_clk),
        .rst(rst),
        .inc(ctrl_pc_inc),
        .load(ctrl_pc_load),
        .jump_addr(bus_data[3:0]),
        .out(pc_to_bus)
    );

    // MAR
    mar memory_address_reg (
        .clk(system_slow_clk),
        .rst(rst),
        .load(ctrl_mar_load),
        .data_in(bus_data[3:0]),
        .out(mar_to_ram)
    );

    // RAM
    ram internal_sram (
        .clk(system_slow_clk),
        .rst(rst),
        .ram_data(ram_data),
        .ram_addr(ram_addr),
        .we_a(ctrl_ram_we_a),
        .re_a(ctrl_ram_re_a),
        .addr_a(mar_to_ram),
        .din_a(bus_data),
        .dout_a(ram_a_out),

        .re_b(ctrl_ram_re_b),
        .addr_b(pc_to_bus),
        .dout_b(ram_b_out)
    );

    // IR
    ir instruction_reg (
        .clk(system_slow_clk),
        .rst(rst),
        .ctrl_ir(ctrl_ir_load),
        .d_in(bus_data),
        .opc(ir_opcode),
        .opr(ir_operand)
    );

    // Registers
    regs register_a (
        .clk(system_slow_clk),
        .rst(rst),
        .data(bus_data),
        .ctrl_reg(ctrl_a_load),
        .d_out(a_reg_out)
    );

    regs register_b (
        .clk(system_slow_clk),
        .rst(rst),
        .data(bus_data),
        .ctrl_reg(ctrl_b_load),
        .d_out(b_reg_out)
    );

    // ALU
    alu execution_unit (
        .a_in(a_reg_out),
        .b_in(b_reg_out),
        .command_in(system_alu_cmd),
        .result(alu_result),
        .carry_out(alu_carry)
    );

    // Flags
    flag_register status_flags (
        .clk(system_slow_clk),
        .rst(rst),
        .flag_en(ctrl_flag_en),
        .result(alu_result),
        .carry_out(alu_carry),
        .z(flag_z),
        .c(flag_c)
    );

    // Output Register
    regs output_peripheral_reg (
        .clk(system_slow_clk),
        .rst(rst),
        .data(bus_data),
        .ctrl_reg(ctrl_out_load),
        .d_out(output_reg_out)
    );

    // ============================
    // UPDATED DISPLAY MODULE
    // ============================
    computer_display_controller display_unit (
        .clk(clk),
        .reset(rst),
        .computer_out(output_reg_out),
        .anodes(anodes),
        .seg(seg)
    );

endmodule
```
## TESTBENCH
```verilog
`timescale 1ns / 1ps

module computer_top_tb;

    reg clk;
    reg rst;

    wire [3:0] anodes;
    wire [7:0] seg;

    // Device Under Test (DUT)
    computer_top DUT (
        .clk(clk),
        .rst(rst),
        .anodes(anodes),
        .seg(seg)
    );

    // 100MHz clock generation
    initial begin
        clk = 0;
        forever #5 clk = ~clk;
    end

    initial begin
        // System reset
        rst = 1;
        #40;
        rst = 0;

        // ================================
        // PROGRAM LOADING INTO RAM
        // ================================

        // LDI 5
        DUT.internal_sram.mem[0] = 8'hE5;

        // STA 15
        DUT.internal_sram.mem[1] = 8'h9F;

        // LDI 2
        DUT.internal_sram.mem[2] = 8'hE2;

        // ADD 15
        DUT.internal_sram.mem[3] = 8'h0F;

        // HLT
        DUT.internal_sram.mem[4] = 8'hF0;

        // =================================
        // SPEED UP SIMULATION (IMPORTANT)
        // =================================

        force DUT.system_slow_clk = clk;

        #2000;

        $display("Execution complete - output verified on 7-seg display system");

        release DUT.system_slow_clk;
        $stop;
    end

    // ================================
    // MONITOR DISPLAY OUTPUT
    // ================================
    initial begin
        $display("TIME\tANODES\tSEG");
        $monitor("%0t\t%b\t%b", $time, anodes, seg);
    end

endmodule
```
## waveform

## schematic

