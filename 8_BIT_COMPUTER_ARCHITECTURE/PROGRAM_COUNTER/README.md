# 🔢 Program Counter (PC)

### Keeping Track of Execution

The Program Counter maintains the address of the instruction currently being fetched. During normal operation it advances sequentially through memory, creating the execution flow of the program. When a branch or jump occurs, the counter can be loaded with a new address, allowing execution to continue from a different location.

### Address Progression

```text
Cycle 1 → Address 0
Cycle 2 → Address 1
Cycle 3 → Address 2
Cycle 4 → Address 3
```

### Supported Actions

| Control Signal | Function |
|---------------|----------|
| inc | Advance to next instruction |
| load | Load jump target |
| rst | Return to address 0 |

### Within the Architecture

Every instruction fetch begins with the Program Counter. Its output is transferred to the Memory Address Register, making it the starting point of the fetch cycle.

## RTL
```verilog
module pc(
    input clk,
    input rst,
    input inc,
    input load,
    input [3:0] jump_addr,
    output [3:0] out
);

reg [3:0] pc;

always @(posedge clk or posedge rst)
begin
    if(rst)
        pc <= 4'b0000;
    else if(load)
        pc <= jump_addr;
    else if(inc)
        pc <= pc + 1'b1;
end

assign out = pc;

endmodule
```
## TESTBENCH
## waveform
## schematic
