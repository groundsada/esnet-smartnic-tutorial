# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

## Lesson 3: P4 Simulation

In this lesson, you will be simulating the behavior of the program that you had written in Lesson 2. Please keep in mind that this is a behavioral simulation. The steps to simulate the hardware behavior are in Lesson 4.

## Example Design: p4_only

The p4_only example design provides the P4 source code and a behavioural simulation testcase for a simple
P4 application core.

This example follows a pure P4 design flow and does NOT include any custom verilog hardware.  As such,
the user only provides the working P4 program file to build the custom SmartNIC hardware.



## Functional Specification

The p4_only design implements a simple table-based Layer-2 packet switch.  It uses a single LPM lookup
table that takes the Ethernet Destination MAC Address field as the key.

When the lookup matches, the table returns the programmed destination port for the specified packet flow.
The returned destination port is then written into the output metadata (`egress_port` field), which is used
by the SmartNIC hardware for packet forwarding.

When the lookup misses, the output metadata remains unchanged and the packet is forwarded to the destination
port that was specified in the packet's input metadata (`egress_port` field).

Packets with invalid and errored Ethernet headers are dropped.



## Development Flow

The sections below direct the user to:

1. Install the SmartNIC platform design repositories. (Lesson 1 and 2)
2. Execute a P4 behavioural simulation (to verify P4 program correctness ahead of building hardware). (Lesson 3)
3. Generate and Simulate the AMD (Xilinx) Vitisnetp4 Example Design at the RTL-level (optional). (Lesson 4)
4. Build the P4-based custom SmartNIC hardware. (Lesson 5)

## Makefile

The execution of P4 behavioural simulations is driven by the Makefile
in the p4/sim/ directory.

This Makefile includes the variable assignments that specify how to run
a p4 behavioural simulation, as well as the name of the p4 file and list
of testcases that should be included in a full simulation run.

Each testcase is captured in a separate subdirectory, which contains the
input and output files for the specified test.  The testcase subdirectories
follow the naming pattern of `test-<NAME>`.

Prior to running a simulation, the user should update the Makefile with the
following variable assignments (all other variable assignments can remain
unchanged):

     P4_SOURCE = <pathname of p4 source file>
     P4BM_DIRS = <testcase subdirectory list>

The Makefile includes execution targets to run (or clean) a single testcase
simulation, or simulation of the full testcase suite.

To simulate a single testcase, execute make with the `P4BM_DIR=` argument
set to the testcase subdirectory of interest.  For example:

     > make P4BM_DIR=test-fwd-p0

Or to simulate all test cases:

     > make sim-all

Note: the `sim-all` target is the default target when make is called without
arguments.

To clean all simulation output products from the p4 directory, type:

     > make clean


## Testcase Input Files

The input stimulus file set for each testcase includes three files:

1. `cli_commands.txt` - Command script to set table entries and initiate input
stimulus. Commands and syntax follow the Xilinx p4bm-vitisnet-cli.

2. `packets_in.user` - Input packet stream user data.  Each byte sequence terminated
by a semicolon (;) represents a packet.  Packets are captured in sequence.  The '%'
character is used to start comments.

    `packets_in.pcap` - PCAP file containing the input packet stream.
                        Alternative format to `packets_in.user` (optional).

3. `packets_in.meta` - Input packet metadata.  Each line corresponds
to a packet in the input PCAP file (in sequence).  The syntax of the metadata
is described in the Xilinx VitisnetP4 documentation.  Note: Each metadata record
must be terminated by a semicolon (;).

For more details, see chapter 3 of *Vitis Networking P4 User Guide, UG1308 (v2023.1) May 16, 2023*.


## Testcase Output Files

1. `packets_out.user` - Output packet stream user data. Same syntax as input
packet stream user data.

   `packets_out.pcap` - PCAP file containing the output packet stream,
                        if packets_in format was `packets_in.pcap`.

2. `packets_out.meta` - Output packet metadata.  Each line corresponds
to a packet in the output PCAP file (in sequence).  Same syntax as input
packet metadata.

Note: An expected/ directory is optionally included to capture the expected
output results of a testcase.  This expected output can be used for
automated regression testing.


## Testcases: p4_only

`test-fwd-p0` - The p4_only design includes a single example testcase called
test-fwd-p0.  This testcase programs a small number of table entries that
forward the specified packet flows to destination port 0.  The input
stimulus includes a single packet on each flow to validate that all packets
are forwarded to port 0.  All other packets are forwarded to the egress_port specified
by the input meta data.

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

## Lesson 4: RTL Simulation

Link: **[Lesson 4: RTL Simulation](4-lesson4.md)**

