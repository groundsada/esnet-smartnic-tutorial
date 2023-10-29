# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

## Lesson 2: The Development Workflow

Welcome to Lesson 2 of our tutorial series on utilizing Xilinx Alveo FPGAs as SmartNICs within the ESnet framework. This step involves writing the P4 applications, testing, debugging, and compiling them to bitfiles that can be run on the AMD/Xilinx Alveo cards. This step makes use of Vivado and VitisNetworkingP4 to provide all the tools needed for your P4 development. The ESnet framework streamlines your P4 development with readily available scripts and makefiles. These resources ensure that you can seamlessly test and compile your P4 logic into bitfiles, allowing you to focus exclusively on your P4 programming. As mentioned above, this step requires special licensing. If you already possess the bitfiles ready for deployment onto the U280 cards, you may proceed directly to the "Deployment Workflow" section.

### Introduction:

The tools required to be able to write, test, simulate and compile P4 programs written for the Xilinx Alveo FPGAs are based on (and included) in the [esnet-smartnic-hw](https://github.com/esnet/esnet-smartnic-hw) repository.

### Repository Structure and Dependencies

The ESnet SmartNIC platform is comprised of a collection of separate modular components,
each maintained in their own git repository.

The platform includes the following repositories:

   - `OpenNIC shell` (https://github.com/esnet/open-nic-shell.git)
     An FPGA-based NIC shell that runs on the AMD (Xilinx) Alveo
     family of hardware boards.  This repository is a fork of a
     Xilinx-provided repository (https://github.com/Xilinx/open-nic-shell.git).
     This repository also includes customizations for the ESnet SmartNIC platform.

   - `ESnet SmartNIC Hardware` (https://github.com/esnet/esnet-smartnic-hw.git)
     Hardware design directory for the ESnet SmartNIC platform.

   - `ESnet SmartNIC Firmware` (https://github.com/esnet/esnet-smartnic-fw.git)
     Firmware design directory for the ESnet SmartNIC platform.

   - `ESnet FPGA library` (https://github.com/esnet/esnet-fpga-library.git)
     General-purpose components and infrastructure for a structured FPGA design methodology.

   - `SVunit` (https://github.com/svunit/svunit.git)
     An open-source framework for FPGA System Verilog
     verification. SVunit is used by the SmartNIC platform, but is
     neither maintained nor distributed by ESnet.

   - `ESnet Regio` (https://github.com/esnet/regio.git)
     Automation tools for the implementation of FPGA register map logic and software code.


All dependent repositories are instantiated in the parent repository as a submodule, as
depicted below:

```
esnet-smartnic-hw/ (parent repository)
├── esnet-fpga-library/ (submodule)
│   └── tools/
│       ├── regio/ (submodule)
│       └── svunit/ (submodule)
└── open-nic-shell/ (submodule)

esnet-smartnic-fw/ (parent repository)
```



### Directory Structure

The directory structure for the ESnet SmartNIC hardware design repository is captured and described below.

```
esnet-smartnic-hw/
├── cfg/
├── config.mk
├── docs/
├── esnet-fpga-library/
├── examples/
├── LICENSE.md
├── Makefile
├── makefile.esnet
├── open-nic-shell/
├── paths.mk
├── README.md
├── scripts/
└── src/

cfg/
  Contains global configuration files for the SmartNIC project.

config.mk
  Sets environment variables for the SmartNIC project.

docs/
  Contains documentation files for the SmartNIC platform.

esnet-fpga-library/
  Contains the ESnet FPGA Design Library (imported as a git submodule).
  This library contains general-purpose FPGA design content.

examples/
  Contains SmartNIC application design exaples.  A new application directory can be started
  by copying one of the provided example directories, or by modeling portions of the example
  directory structure.

LICENSE.md
  Contains the licensing terms and copyright notice for this repository.

Makefile
  SmartNIC platform Makefile.  Used to build the FPGA bitfile for the target application,
  as well as generate all artifacts necessary for firmware integration on the hardware platform.

makefile.esnet
  OpenNIC shell Makefile.
  Used to build the AMD (Xilinx) open-nic-shell for the target application.

open-nic_shell/
  Contains the AMD (Xilinx) OpenNIC Shell repository (imported as a git submodule).
  OpenNIC shell delivers an FPGA-based NIC shell with 100Gbps Ethernet ports,
  for use on the AMD (Xilinx) Alveo platform.

paths.mk
  Describes paths to resources provided by the SmartNIC project.

README.md
  This README file.

scripts/
  Contains SmartNIC platform scripts, for application configuration.

src/
  Contains RTL source and verification code for SmartNIC platform FPGA design components,
  captured in System Verilog.
```

### Installing the SmartNIC Hardware Design Repository

The following steps guide a new user through the installation of the
SmartNIC Hardware Design Repository, beginning with a
suitably-configured host running Ubuntu 20.04 LTS Linux.

1. Install the esnet-smartnic-hw respository by creating a clone from github into a local directory:

       > git clone https://github.com/esnet/esnet-smartnic-hw.git


2. Initialize all submodules within the esnet-smartnic-hw/ design directory:

       > cd esnet-smartnic-hw
       > git submodule update --init --recursive


3. Install the prerequisites required to run the esnet regio tools:

       > sudo apt install python3-yaml python3-jinja2 python3-click
       > pip3 install -r esnet-fpga-library/tools/regio/requirements.txt

   Note: The above instructions and more details about the `regio` tools can be found in the README file
   at: `esnet-fpga-library/tools/regio/README.md`

### Building the SmartNIC p4_only Example Design

You can build the `p4_only` example design by executing its Makefile. Follow these steps:

1. Navigate to the p4_only example design directory:
   ```shell
   > cd examples/p4_only
   ```

2. Modify the Makefile to ensure that `export BOARD` is set to the target card (i.e au280 or au55c):
   ```shell
   > export BOARD := au280
   ```

3. Make sure to source the Vivado environment script:
   ```shell
   > source /tools/Xilinx/Vivado/2023.1/settings.64.sh
   ```
   At the time of writing this guide, 2023.1 was the version supported by the ESnet framework. Please refer to the README on the ESnet repositories.

4. Run the Makefile to build the design:
   ```shell
   > make
   ```

Upon completion, you'll find an artifact zipfile with the default pathname: `artifacts/<BUILD_NAME>/artifacts.<BOARD>.<BUILD_NAME>.0.zip`. This artifact zipfile contains all necessary hardware artifacts, including the bitfile, firmware driver files, regmap YAML files, the source P4 file, and any Wireshark .lua files.

For more details about the `p4_only` design and simulating the P4 program, refer to the `examples/p4_only/README.md` file.

You can find more information on the ESnet development workflow here: [ESnet Development Workflow](https://github.com/esnet/esnet-smartnic-hw).


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

7. Make sure to source the Vivado environment script:
   ```shell
   > source /tools/Xilinx/Vivado/2023.1/settings.64.sh
   ```
   At the time of writing this guide, 2023.1 was the version supported by the ESnet framework. Please refer to the README on the ESnet repositories.

8. Build the design by executing the copied application Makefile:

       > make

9. To simulate the P4 program, refer to the readme file provided in the p4/sim/ directory i.e. `p4/sim/README.md`




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


### P4 Simulation: p4_only

The p4_only example design provides the P4 source code and a behavioural simulation testcase for a simple
P4 application core.

This example follows a pure P4 design flow and does NOT include any custom verilog hardware.  As such,
the user only provides the working P4 program file to build the custom SmartNIC hardware.



### Functional Specification

The p4_only design implements a simple table-based Layer-2 packet switch.  It uses a single LPM lookup
table that takes the Ethernet Destination MAC Address field as the key.

When the lookup matches, the table returns the programmed destination port for the specified packet flow.
The returned destination port is then written into the output metadata (`egress_port` field), which is used
by the SmartNIC hardware for packet forwarding.

When the lookup misses, the output metadata remains unchanged and the packet is forwarded to the destination
port that was specified in the packet's input metadata (`egress_port` field).

Packets with invalid and errored Ethernet headers are dropped.



### Makefile

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

#### Terminal Configuration

If you encounter the following warning when using rlwrap:

```
rlwrap: warning: your $TERM is 'xterm-256color' but rlwrap couldn't find it in the terminfo database. Expect some problems.
```

You can resolve this issue by adding the following line to your .profile file:

```
export TERM=xterm1
```

### Testcase Input Files

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


### Testcase Output Files

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


### Testcases: p4_only

`test-fwd-p0` - The p4_only design includes a single example testcase called
test-fwd-p0.  This testcase programs a small number of table entries that
forward the specified packet flows to destination port 0.  The input
stimulus includes a single packet on each flow to validate that all packets
are forwarded to port 0.  All other packets are forwarded to the egress_port specified
by the input meta data.

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

None to date. If you face any issues, please contact [mailto:cs595-f2023-group@iit.edu](mailto:cs595-f2023-group@iit.edu)

## Lesson 3: The Deployment Workflow

Link: **[Lesson 3: The Deployment Workflow](3-lesson3.md)**

