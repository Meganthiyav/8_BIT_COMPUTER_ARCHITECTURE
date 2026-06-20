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

