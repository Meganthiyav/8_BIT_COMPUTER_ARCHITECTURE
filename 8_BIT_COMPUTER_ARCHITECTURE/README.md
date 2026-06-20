# 🧠 8-Bit Computer Architecture 

## 📌 Description

** The instruction set for this architecture is user-defined and will be provided externally. It is not fixed within the design and can be customized based on application requirements. The control unit is designed to decode and execute the provided instructions accordingly.**


## ⚙️ Specifications

* 8-bit data path architecture
* Modular Verilog HDL implementation
* FSM-based Control Unit for instruction sequencing
* Single shared internal bus system
* Fetch–Decode–Execute instruction cycle
* Dual-port RAM (16 × 8 configuration)
* Arithmetic Logic Unit supporting arithmetic and logical operations
* Flag register for status indication (Zero, Carry, etc.)
* Fully synchronous digital system design

---

## 🧩 System Modules

* Program Counter (PC)
* Memory Address Register (MAR)
* Dual Port RAM (16x8)
* Instruction Register (IR)
* Control Unit (FSM)
* Arithmetic Logic Unit (ALU)
* A Register (Accumulator)
* B Register
* Flag Register
* Output Register
* Bus System

---

## 🧱 Architecture Overview (Block Diagram)
<img width="1510" height="888" alt="image" src="https://github.com/user-attachments/assets/c6154510-bc9f-4e62-a64f-76bedfaf6fac" />


## 🔄 Instruction Cycle

* **T0:** PC → MAR
* **T1:** RAM → IR, PC increment
* **T2:** Instruction decode (Control Unit)
* **T3:** Operand fetch (if required)
* **T4+:** Execution via ALU / register transfer

---     
## ⚙️ Development Platform
FPGA Board: Spartan-7 Boolean FPGA Board

Design Language: Verilog HDL

Design Methodology: RTL (Register Transfer Level)
