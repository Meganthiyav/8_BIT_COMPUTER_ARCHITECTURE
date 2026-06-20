# 📦 Register Module

## 📌 Overview

This module implements an **8-bit synchronous register** used as a common storage element within the 8-bit computer architecture. The same register module is instantiated for the **A Register**, **B Register**, and **Output Register**, providing a reusable and modular design approach.

The register captures input data on the rising edge of the clock when the control signal is asserted and retains the stored value until the next valid write operation or reset.

---

## ⚙️ Specifications

* 8-bit data width
* Positive-edge triggered operation
* Synchronous data loading
* Reset support
* Control signal based write operation
* Reusable implementation for multiple register instances

---

## 🔌 Ports

| Port     | Direction | Width | Description           |
| -------- | --------- | ----- | --------------------- |
| clk      | Input     | 1     | System clock          |
| rst      | Input     | 1     | Reset signal          |
| data     | Input     | 8     | Data input            |
| ctrl_reg | Input     | 1     | Register load enable  |
| d_out    | Output    | 8     | Stored register value |

---

## 🔄 Operation

* When `rst` is asserted, the register is cleared.
* When `ctrl_reg` is asserted, the input data is loaded into the register on the rising edge of the clock.
* When `ctrl_reg` is deasserted, the previously stored value is retained.

---

## 🏛️ Usage in Architecture

### A Register

Stores operands and intermediate ALU results.

### B Register

Stores secondary operands required for arithmetic and logical operations.

### Output Register

Stores the final processed result before external output.

---

## 🧩 Design Approach

A single generic register module is utilized throughout the architecture to implement multiple storage registers. This approach improves design modularity, reduces code duplication, and simplifies maintenance.

---

This module serves as the common building block for the **A Register**, **B Register**, and **Output Register** within the 8-bit computer architecture.

## RTL

```verilog
`timescale 1ns / 1ps


module regs(
 input clk ,
 input rst,
 input [7:0] data,
 input ctrl_reg,
 output  [7:0]d_out
    );
    reg [7:0] regs;
    always @(posedge clk)
    begin
    if(rst)
    regs <= 8'b0;
    else if(ctrl_reg)
        regs <= data;
    
     end
        
       assign d_out = regs;
endmodule
```
   
## TESTBENCH
```verilog
`timescale 1ns / 1ps

module regs_tb;

    reg clk;
    reg rst;
    reg [7:0] data;
    reg ctrl_reg;
    wire [7:0] d_out;

    // DUT Instantiation
    regs DUT (
        .clk(clk),
        .rst(rst),
        .data(data),
        .ctrl_reg(ctrl_reg),
        .d_out(d_out)
    );

    // Clock Generation (10 ns period)
    initial begin
        clk = 0;
        forever #5 clk = ~clk;
    end

    // Test Stimulus
    initial begin

        // Initialize inputs
        rst = 1;
        ctrl_reg = 0;
        data = 8'h00;

        #10;
        rst = 0;

        // Load first value
        data = 8'hAA;
        ctrl_reg = 1;
        #10;

        // Load second value
        data = 8'h55;
        #10;

        // Disable loading (register should hold value)
        ctrl_reg = 0;
        data = 8'hF0;
        #10;

        // Enable loading again
        ctrl_reg = 1;
        data = 8'h0F;
        #10;

        // Apply reset
        rst = 1;
        #10;
        rst = 0;

        #20;
        $finish;

    end

endmodule
```
## Waveform 
<img width="1168" height="660" alt="image" src="https://github.com/user-attachments/assets/1b94d0b0-8a3f-4395-a1ae-ed479a88359c" />

## Schematic
<img width="675" height="652" alt="image" src="https://github.com/user-attachments/assets/be686363-bc19-49a0-a917-5dc60a72863f" />
