# Synchronous FIFO UVM Verification Environment

This repository contains a complete Universal Verification Methodology (UVM) testbench used to verify a synchronous parameterizable FIFO design. 

## Design Specifications (RTL)
* **Depth:** 16
* **Data Width:** 8-bit
* **Features:** 0-cycle latency for seamless reading, internal count tracking, and hardware protection against writing while `full` or reading while `empty`.

## UVM Testbench Architecture
The verification environment is built using SystemVerilog and UVM 1.2. 

* **Sequence Item:** Randomized `wr_en`, `rd_en`, and 8-bit `data_in`. Includes constraints to prevent simultaneous read/write assertions in the same exact cycle for strict state testing.
* **Sequences:**
  * `fifo_sequence`: Drives 20 random transactions.
  * `fifo_full_sequence`: A directed sequence that forces 16 back-to-back writes to intentionally drive the FIFO into a `full` state, followed by an extra write to verify hardware protection.
* **Driver & Monitor:** Synchronized to the clock edge, ensuring precise sampling of interface signals.
* **Scoreboard:** Implements a reference queue to check data integrity. Crucially, it tracks expected behavioral drops: it accurately flags a **PASS** when a write attempt is correctly blocked by the DUT during a `full` condition, avoiding false UVM_ERRORs.

## Simulation & Waveform Results
The testbench was compiled and simulated using Xilinx Vivado. The simulation successfully passes the directed full-sequence test followed by the randomized sequence test.

### Initial Reset and Write Sequence
The waveform below demonstrates the reset sequence and the initial randomized data inputs driving the write pointers (`wr_ptr`) and internal count up.


### FIFO Full Condition & Edge Case Handling
This section of the waveform highlights the FIFO reaching its maximum depth (`count` = 10 in hex, which is 16). The UVM scoreboard correctly identifies that the `full` flag is asserted and verifies that subsequent write attempts are safely dropped by the RTL, preventing data corruption.



## How to Run
1. Ensure you have a SystemVerilog simulator with UVM 1.2 support (e.g., Vivado, Questa, VCS).
2. Compile the design and testbench files.
3. Run the top-level module: `tb_top`
4. The test executed by default is `fifo_test`.
