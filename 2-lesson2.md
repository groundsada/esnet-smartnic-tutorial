# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

## Lesson 2: esnet-smartnic-hw

This lesson builds on following the steps in Lesson 1.

### Building the SmartNIC p4_only Example Design

Build the `p4_only` example design by executing the p4_only application Makefile.
From the esnet-smartnic-hw directory:

       > cd examples/p4_only
       > make

   Upon completion, the above step creates an artifact zipfile with the default pathname:
   `artifacts/<BUILD_NAME>/artifacts.<BOARD>.<BUILD_NAME>.0.zip`

   This artifact zipfile contains all of the necessary h/w artifacts to integrate with the firmware.
   In addition to the bitfile, it includes firmware driver files, regmap yaml files, the source p4 file,
   and any wireshark .lua files.

   For more details about the `p4_only` design, as well as simulating the P4 program,  refer to
   `examples/p4_only/README.md`.


### Building a New P4 Application

The following steps can be taken by a new user to setup a local application design directory for building
the bitfile and artifacts for a custom P4-based SmartNIC application.

1. Install the esnet-smartnic-hw respository (as described above).

   Or, alternatively, add the esnet-smartnic-hw respository to an existing git repository as a sub-module:

       > git submodule add https://github.com/esnet/esnet-smartnic-hw.git

2. Initialize all submodules within the esnet-smartnic-hw/ design directory:

       > cd esnet-smartnic-hw
       > git submodule update --init --recursive

3. Install Vivado and configure the runtime environment (as described above).

4. Return  to the local application design directory and then copy the example SmartNIC application Makefile and p4/ sub-directory into the local application design directory:

       > cd ../
       > cp esnet-smartnic-hw/examples/p4_only/Makefile ./
       > cp -r esnet-smartnic-hw/examples/p4_only/p4 ./

5. Using a preferred editor, edit the copied Makefile to update the SMARTNIC_DIR environment variable assignment as follows:

       #SMARTNIC_DIR := ../..
       SMARTNIC_DIR := $(CURDIR)/esnet-smartnic-hw

6. Copy the application p4 file to the following location and filename:

       > cp <p4_filename> p4/`basename $PWD`.p4

     Note: By default, the SmartNIC scripts take the basename of the application design directory to be the name of the application, as well as its associated filenames.

7. Build the design by executing the copied application Makefile:

       > make

8. To simulate the P4 program, refer to the readme file provided in the p4/sim/ directory i.e. `p4/sim/README.md`




### P4 Programming Requirements

The P4 processing core of the SmartNIC platform is implemented with the AMD (Xilinx) VitisNetP4 IP core.
In order to meet the requirements of the VitisNetP4 IP core and the SmartNIC platform, a new P4 program should
consider the following guidelines.


#### AMD (Xilinx) P4 Architecture:

The Vitis Networking P4 compiler supports a specific pipelined datapath architecture that is comprised of 3
customizable stages: A Parser Engine, followed by a Match-Action Engine, followed by a Deparser Engine.
User P4 files **MUST** be structured to comply with this processing architecture, and specify the custom operations
desired within each of these processing stages.

More details about the AMD (Xilinx) P4 architecture can be found in the *Vitis Networking P4 User Guide, UG1308
(v2023.1) May 16, 2023*.


#### Include files:

The P4 program **MUST** include the following AMD (Xilinx) VitisNetP4 include files:

      #include <core.p4>
      #include <xsa.p4>

These files capture built-in constructs and the standard definitions for the AMD (Xilinx) P4 architecture.
They are located in the Vivado installation directory at:
`$XILINX_VIVADO/data/ip/xilinx/vitis_net_p4_v1_1/include/p4/`


#### Interfaces:

The P4 processing core supports 3 types of interfaces:

- *Packet Ports* are the primary interfaces responsible for moving packets in and out of the core, as well as
between engines.  Engines can only contain a single input packet port and a single output packet port.

- *Metadata Ports* carry sideband data related to a packet. Metadata can only correspond to a single packet
and is processed in parallel with the packet.  More on Metadata below.

- *Register IO Ports (axi4l)* are used to control the contents of the Look-up engines.


#### Metadata Definitions:

The VitisNetP4 core supports both `Standard Metadata` (defined and set by the P4 core), and `User Metadata`
(defined and set by the SmartNIC platform).  Both types of metadata can be read and/or written by the P4
program.

For more details about the `Standard Metadata` definitions, see *Vitis Networking P4 User Guide, UG1308
(v2023.1) May 16, 2023*.

In order for the compiled VitisNetP4 core to match the SmartNIC application interface, a user P4 program **MUST**
define the User Metadata structure as follows:

    struct smartnic_metadata {
        bit<64> timestamp_ns;    // 64b timestamp (in nanoseconds). Set at packet arrival time.
        bit<16> pid;             // 16b packet id used by platform (READ ONLY - DO NOT EDIT).
        bit<3>  ingress_port;    // 3b ingress port (0:CMAC0, 1:CMAC1, 2:HOST0, 3:HOST1).
        bit<3>  egress_port;     // 3b egress port  (0:CMAC0, 1:CMAC1, 2:HOST0, 3:HOST1).
        bit<1>  truncate_enable; // reserved (tied to 0).
        bit<16> truncate_length; // reserved (tied to 0).
        bit<1>  rss_enable;      // reserved (tied to 0).
        bit<12> rss_entropy;     // reserved (tied to 0).
        bit<4>  drop_reason;     // reserved (tied to 0).
        bit<32> scratch;         // reserved (tied to 0).
    }

#### Lookup Engines and Externs:

In order for the compiled VitisNetP4 core to match the SmartNIC application
interface, a user Program **MUST** have:

- One (or more) Look-up Engines.
- No HBM BCAMs.
- No Externs.

Support for these features may be added in a future release.


### The tl;dr

You can simply do: `cd examples/p4_only/p4` and here you can modify the p4 program. If you run `make` it will compile your P4 program into the Xilinx FPGA board model specified in `Makefile`.

In Lesson 3, you will learn how to simulate the behavior of your program.


### References

This tutorial is built on the following software/respositories along with versions/commits:

- Ubuntu 20.04 with Linux 5.4.0-153.
- Vivado 2023.1 with the VitisNetowrkingP4 license.
- The [esnet-smartnic-hw](https://github.com/esnet/esnet-smartnic-hw) repository (commit: 9ee2cbb).
- The [esnet-smartnic-fw](https://github.com/esnet/esnet-smartnic-fw) repository (commit: 180595c).
- The [smartnic-dpdk-docker](https://github.com/esnet/smartnic-dpdk-docker) repository (commit: a52dba3).
- The [xilinx-labtools-docker](https://github.com/esnet/xilinx-labtools-docker) repository (commit: 84cf05f).

### Known Issues

None to date. If you face any issues, please contact [msada@hawk.iit.edu](mailto:msada@hawk.iit.edu)

## Lesson 3: P4 Simulation

Link: **[Lesson 3: P4 Simulation](3-lesson3.md)**

