# Synchronous FIFO Buffer: RTL Design & Functional Verification

## Overview
This project features the design and verification of a **Synchronous FIFO (First-In-First-Out) Buffer** implemented in Verilog HDL. In modern System-on-Chip (SoC) architectures, synchronous FIFOs are critical for rate-matching and data buffering between logic blocks operating within the same clock domain. This implementation focuses on efficient pointer management and robust status flag generation to ensure data integrity during high-throughput operations.

---

## Design Specifications
The FIFO is designed with a modular architecture, utilizing a circular buffer (ring buffer) topology to manage fixed-size memory without the overhead of shifting data.

### Signal Descriptions
| Signal | Direction | Width | Description |
| :--- | :--- | :--- | :--- |
| `clk` | Input | 1 | System Clock (Rising edge active) |
| `rst` | Input | 1 | Asynchronous Reset (Active high) |
| `wr_en` | Input | 1 | Write Enable: Initiates data enqueue |
| `rd_en` | Input | 1 | Read Enable: Initiates data dequeue |
| `buf_in` | Input | [1:0] | 2-bit Data Input Bus |
| `buf_out` | Output | [1:0] | 2-bit Data Output Bus |
| `buf_empty` | Output | 1 | Asserted when the buffer contains no data |
| `buf_full` | Output | 1 | Asserted when the buffer reached maximum capacity |

---

## Architecture
The design utilizes a **Ring Buffer** data structure consisting of a fixed-size array and two primary pointers.

### Core Components:
* **Dual-Port Memory Array:** A 4-slot internal memory (`buf_mem`) acting as the primary storage medium.
* **Pointer Logic:** Independent 2-bit binary counters (`wr_ptr` and `rd_ptr`) track the write and read addresses.
* **Flag Generation:** A 3-bit `fifo_counter` tracks the number of elements (0 to 4) to provide real-time status of `buf_full` and `buf_empty` flags.
* **Write/Read Control:** Sequential logic ensures that the write pointer only increments during a valid `wr_en` when the buffer is not full, and the read pointer only increments during a valid `rd_en` when not empty.

---

## Verification Strategy
The verification plan was executed using a directed testbench (`FIFO_tb`) to ensure 100% functional coverage of the FIFO’s operational states.

### Test Scenarios Covered:
* **Reset Recovery:** Verified that `rst` correctly initializes pointers and the counter to zero, asserting `buf_empty`.
* **Linear Write/Read:** Sequential enqueueing of data (values 2'b01, 2'b10, 2'b11, 2'b00) until `buf_full` is reached, followed by a full dequeue.
* **Overflow Protection:** Verified that writing to a full buffer results in the data being ignored, maintaining the existing queue status.
* **Underflow Protection:** Verified that reading from an empty buffer does not result in invalid pointer increments.
* **Wrap-around Logic:** Confirmed that pointers correctly wrap from address 3 back to 0.

---

## Simulation Results
<img width="970" height="388" alt="waveform0_80" src="https://github.com/user-attachments/assets/c96c1aa2-6ef6-4711-9762-91f91f37188b" />
<img width="970" height="402" alt="waveform80_160" src="https://github.com/user-attachments/assets/a4859511-6eea-400d-9a8a-3abf4f8d2436" />

### Waveform Analysis:
* **Initialization:** At `T=0ns`, `rst` is asserted, bringing `buf_empty` to high.
* **Write Cycle:** From `20ns` to `60ns`, multiple write operations occur. The `fifo_counter` increments, and `buf_out` remains stable until a read is initiated.
* **Read Cycle:** At `100ns`, `rd_en` is asserted. The waveform demonstrates the FIFO principle: the first value written is the first value appearing on `buf_out`.

---

## Tools Used
* **Language:** Verilog HDL
* **Simulator:** ModelSim / Vivado

---

### Implementation Snippet (Module Header)
```verilog
module FIFO(
    input rst, clk, wr_en, rd_en,
    input [1:0] buf_in,
    output reg [1:0] buf_out,
    output reg buf_empty, buf_full
);
// Logic for pointer management and flag generation
endmodule
