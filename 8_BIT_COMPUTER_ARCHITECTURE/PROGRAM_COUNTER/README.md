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
## TESTBENCH
## waveform
## schematic
