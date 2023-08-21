# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

## Lesson 4: RTL Simulation

In this lesson, you will learn to generate and simulate the AMD (Xilinx) Vitisnetp4 example design at the RTL-level

### Generating and Simulating the AMD (Xilinx) Vitisnetp4 Example Design

1. Prior to generating the AMD (Xilinx) vitisnetp4 example design, a user must specify the p4bm test directory
that will be imported for simulation.  This is done by assigning the full directory pathname to the
EXAMPLE_TEST_DIR variable in the application Makefile. For example:

       export EXAMPLE_TEST_DIR := $(CURDIR)/p4/sim/test-fwd-p0

   By setting the above Makefile variable, the example design will import all input stimulus, CLI programming,
and any (optional) extern behvioural models associated with the specified simulation testcase.


2. The AMD (Xilinx) vitisnetp4 example design can be generated in the local application design directory by
running the 'example' target of the root-level application Makefile, as follows:

       > make example

   Executing the above make command will generate the vitisnetp4 example design in a subdirectory
called `example/sdnet_0_ex/`.


3. From the `example/sdnet_0_ex/` subdirectory, the AMD (Xilinx) Vivado tool can be invoked, the
example design project can be opened, and the p4 processor can be simulated, as follows:

       > cd example/sdnet_0_ex
       > vivado

       - From the `File->Project->Open...` menu, select 'sdnet_0_ex.xpr' and open the example design project.
       - From the `Flow Navigator` menu, select 'Simulation->Run Simulation->Run Behavioural Simulation'.

   For more information about how to simulate designs and evaluate results within the AMD (Xilinx) Vivado GUI,
refer to the following document:

   - *Vivado Design Suite User Guide - Logic Simulation, UG900 (v2023.1) May 10, 2023.*


4. Note that vitisnetp4 example design generation supports the optional instantiation of custom user extern
function(s) by including the following design files:

   - System Verilog RTL code for custom extern function(s) in the file `extern/rtl/smartnic_extern.sv`.
  Furthermore, if a user captures extern function(s) in a design hierarchy comprised of multiple .sv files,
  all of the .sv files located in the `extern/rtl` directory will be included in the example design project,

   - C++ behavioural model(s) for custom extern function(s) in the file `p4/sim/user_externs/smartnic_extern.cpp`

   Furthermore, when simulating the vitisnetp4 example design with a user extern, the `smartnic_extern`
instantiation is located within the `example_dut_wrapper` module (instance name `dut_inst/smartnic_extern_0`).

### References

This tutorial is built on the following software/respositories along with versions/commits:

- Ubuntu 20.04 with Linux 5.4.0-153.
- Vivado 2023.1 with the VitisNetowrkingP4 license.
- The [esnet-smartnic-hw](https://github.com/esnet/esnet-smartnic-hw) repository (commit: 9ee2cbb).
- The [esnet-smartnic-fw](https://github.com/esnet/esnet-smartnic-fw) repository (commit: 180595c).
- The [smartnic-dpdk-docker](https://github.com/esnet/smartnic-dpdk-docker) repository (commit: a52dba3).
- The [xilinx-labtools-docker](https://github.com/esnet/xilinx-labtools-docker) repository (commit: 84cf05f).

### Known Issues

None to date. If you face any issues, please contact [https://groups.google.com/a/iit.edu/g/cs595-f2023-group](mailto:https://groups.google.com/a/iit.edu/g/cs595-f2023-group)

## Advanced Lesson 5: The ESnet stack

**Please note that from Lesson 5 forward, we cover running compiled P4 program bitfile on FPGAs.**

Link: **[Advanced Lesson 5: The ESnet Stack](5-lesson5.md)**

