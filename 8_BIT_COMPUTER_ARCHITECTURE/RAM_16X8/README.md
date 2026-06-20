# 💾 Dual-Port RAM (16 × 8)

## The Memory Core of the Processor

The RAM module serves as the primary storage subsystem of the 8-bit computer architecture, housing both program instructions and operational data. Every instruction executed by the processor originates from this memory space, making it the foundation upon which the entire system operates.

Designed with a dual-port architecture, the memory allows instruction fetching and data access to occur independently, enabling smoother processor operation and simplifying datapath control.

---

## Memory Organization

The memory consists of 16 addressable locations, each capable of storing an 8-bit value.

| Parameter      | Specification |
| -------------- | ------------- |
| Memory Type    | Dual-Port RAM |
| Address Width  | 4 Bits        |
| Data Width     | 8 Bits        |
| Memory Depth   | 16 Locations  |
| Total Capacity | 128 Bits      |


---

## Dual-Port Architecture

Unlike conventional single-port memories, this design provides two independent access interfaces.

### 🔹 Port A — Data Access Port

Port A is responsible for processor data operations and supports both read and write functionality.

**Capabilities**

* Memory Read
* Memory Write
* Data Modification
* Register Data Storage

---

### 🔹 Port B — Instruction Fetch Port

Port B is dedicated to instruction retrieval during the fetch stage of the processor.

**Capabilities**

* Instruction Fetch
* Read-Only Access
* Independent Operation from Port A

---

## Concurrent Memory Access

The dual-port design allows multiple memory activities to occur without resource conflicts.

```text
Instruction Fetch
        ↓
      Port B
        ↓
       RAM
        ↑
      Port A
        ↑
 Data Read / Write
```

This separation enables instruction retrieval while data transactions are simultaneously performed.

---

## Data Forwarding Mechanism

To ensure data consistency, the memory incorporates forwarding logic.

When a write operation and read operation target the same address during the same cycle, the newly written value is forwarded directly to the output rather than waiting for the memory array to update.

```text
Write Request
      ↓
Same Address Read?
      ↓
     Yes
      ↓
Forward New Data
      ↓
Immediate Output
```

This mechanism prevents stale data from being observed during simultaneous access conditions.

---

## Integration Within the Fetch–Decode–Execute Cycle

The RAM participates in every stage of processor operation.

### Instruction Fetch

```text
Program Counter
       ↓
      MAR
       ↓
      RAM
       ↓
Instruction Register
```

### Data Access

```text
Register
    ↓
 Address
    ↓
  RAM
    ↓
 Processor Datapath
```

---

## Architectural Significance

The RAM functions as the processor's central knowledge repository, storing both executable instructions and runtime data. Its dual-port organization enables efficient instruction retrieval alongside data manipulation, while forwarding logic guarantees reliable operation under concurrent access conditions.

As the only long-term storage element within the architecture, the RAM forms the backbone of program execution, connecting the Control Unit, datapath, and instruction cycle into a unified computing system.

## RTL

module ram (
    input clk,
    input rst,
    input ctrl_ram,
    input we_a,
    input re_a,
    input [3:0] addr_a,
    input [7:0] din_a,
    output reg [7:0] dout_a,
    input we_b,
    input re_b,
    input [3:0] addr_b,
    input [7:0] din_b,
    output reg [7:0] dout_b
);

    reg [7:0] mem [15:0];

    always @(posedge clk) begin
        if (rst) begin
            dout_a <= 8'b0;
        end else if (ctrl_ram) begin
            if (we_a)
                mem[addr_a] <= din_a;
            if (re_a)
                dout_a <= mem[addr_a];
        end
    end

    always @(posedge clk) begin
        if (rst) begin
            dout_b <= 8'b0;
        end else begin
            if (we_b)
                mem[addr_b] <= din_b;
            if (re_b)
                dout_b <= mem[addr_b];
        end
    end

endmodule

## TESTBENCH

`timescale 1ns / 1ps

module ram_tb;

    reg clk;
    reg rst;
    reg ctrl_ram;
    reg we_a;
    reg re_a;
    reg [3:0] addr_a;
    reg [7:0] din_a;
    wire [7:0] dout_a;

    reg we_b;
    reg re_b;
    reg [3:0] addr_b;
    reg [7:0] din_b;
    wire [7:0] dout_b;

    ram DUT (
        .clk(clk),
        .rst(rst),
    .ctrl_ram(ctrl_ram),
        .we_a(we_a),
        .re_a(re_a),
        .addr_a(addr_a),
        .din_a(din_a),
        .dout_a(dout_a),

        .we_b(we_b),
        .re_b(re_b),
        .addr_b(addr_b),
        .din_b(din_b),
        .dout_b(dout_b)
    );

    always #5 clk = ~clk;

    initial begin
        clk = 0;
        rst = 1;
    ctrl_ram=0;
        we_a = 0; re_a = 0;
        we_b = 0; re_b = 0;
        addr_a = 0; din_a = 0;
        addr_b = 0; din_b = 0;

        #10 rst = 0;

       ctrl_ram = 1;
        #10;
        we_a = 1;
        addr_a = 4'd2;
        din_a = 8'hAA;

        #10 we_a = 0;

        #10;
        re_a = 1;
        addr_a = 4'd2;

        #10 re_a = 0;

        #10;
        we_b = 1;
        addr_b = 4'd2;
        din_b = 8'h55;

        #10 we_b = 0;

        #10;
        re_b = 1;
        addr_b = 4'd2;

        #20;

        $stop;
    end

endmodule

## WAVEFORM
<img width="1475" height="673" alt="image" src="https://github.com/user-attachments/assets/dc61742a-7998-42ae-9a86-0d21a4dc04ea" /> 


 
## SCHEMATIC
<img width="692" height="985" alt="image" src="https://github.com/user-attachments/assets/14e55a07-7760-4bfb-9ed5-e6fbafa7595d" />

