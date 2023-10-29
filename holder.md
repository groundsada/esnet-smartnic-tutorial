## Lesson 1: Getting Started

Welcome to the tutorial on utilizing Xilinx Alveo FPGAs as SmartNICs within the ESnet framework. In this tutorial, we'll guide you through the process of setting up, configuring, and utilizing the power of Xilinx Alveo FPGAs for network acceleration using the ESnet framework.







## Lesson 2: esnet-smartnic-hw

This lesson builds on following the steps in Lesson 1.


In Lesson 3, you will learn how to simulate the behavior of your program.


## Lesson 3: P4 Simulation

In this lesson, you will be simulating the behavior of the program that you had written in Lesson 2. Please keep in mind that this is a behavioral simulation. The steps to simulate the hardware behavior are in Lesson 4.

## P4 Simulation: p4_only

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


## Advanced Lesson 5: The ESnet stack

In this lesson, you will learn how to build 2 of the 3 components that make the ESnet stack.

Setting up the build environment
================================

The smartnic firmware build depends on `docker` and the `docker compose` plugin.

Docker
------

Install Docker on your system following the instructions found here for the **linux** variant that you are using
* https://docs.docker.com/engine/install/

Ensure that you follow the post-install instructions here so that you can run docker **without sudo**
* https://docs.docker.com/engine/install/linux-postinstall/

Verify your docker setup by running this as an ordinary (non-root) user without using `sudo`
```
docker run hello-world
```

Docker Compose
==============

The `docker-compose.yml` file for the smartnic build and the sn-stack depends on features that are only supported in the compose v2 plugin.

Install the `docker compose` plugin like this for a single user:

```
mkdir -p ~/.docker/cli-plugins/
curl -SL https://github.com/docker/compose/releases/download/v2.17.2/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose
```

Alternatively, you can install the `docker compose` plugin system-wide like this:
```
sudo mkdir -p /usr/local/lib/docker/cli-plugins
sudo curl  -o /usr/local/lib/docker/cli-plugins/docker-compose -SL https://github.com/docker/compose/releases/download/v2.17.2/docker-compose-linux-x86_64
sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
```

Verify your docker compose installation by running this as an ordinary (non-root) user without using `sudo`.  For this install, the version output should be
```
$ docker compose version
Docker Compose version v2.17.2
```

Git Submodules
--------------
Ensure that all submodules have been pulled.

```
git submodule init
git submodule update
```

Building a new firmware image
=============================


Install Smartnic Hardware Build Artifact
----------------------------------------

The firmware build depends on the result of a smartnic hardware (FPGA) build.  This file must be available prior to invoking the firmware build.

This file will be called `artifacts.<board>.<app_name>.0.zip` and should be placed in the `sn-hw` directory in your source tree before starting the firmware build.


Create 'smartnic-dpdk-docker' Image
-----------------------------------

- Clone the repository:
```
git clone https://github.com/esnet/smartnic-dpdk-docker.git
```
- Installing git submodules
```
git submodule update --init --recursive
```
- Building the smartnic-dpdk-docker container
```
docker build --pull -t smartnic-dpdk-docker:${USER}-dev .
docker image ls
```



Create 'xilinx-labtools-docker' Image
-------------------------------------

- Clone the repository:
```
git clone https://github.com/esnet/xilinx-labtools-docker.git
```
- Download the Xilinx Labtools Installer
  * Open a web browser to this page: https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/vivado-design-tools/2023-1.html
  * Under the `Vivado Lab Solutions - 2023.1` section
    * Download `Vivado 2023.1: Lab Edition - Linux`
    * Save the file as exactly: `Xilinx_Vivado_Lab_Lin_2023.1_0507_1903.tar.gz`
  * Move the file into the `vivado-installer` directory in this repo

```
$ tree
.
├── Dockerfile
├── sources.list.focal
└── vivado-installer
    ├── install_config_lab.2023.1.txt
    └── Xilinx_Vivado_Lab_Lin_2023.1_0507_1903.tar.gz   <------- put the installer here
```

- Building the xilinx-labtools container

```
docker build --pull -t xilinx-labtools-docker:${USER}-dev .
docker image ls
```

- You should see an image called `xilinx-labtools-docker` with tag `${USER}-dev`.


## Advanced Lesson 6: esnet-smartnic-fw

In this lesson, you will learn how to build the third and last component of the ESnet stack.

Building a new firmware image
=============================


Clone the esnet-smartnic-fw repository
----------------------------------------
`git clone https://github.com/esnet/esnet-smartnic-fw.git`

Git Submodules
--------------
Ensure that all submodules have been pulled.

```
git submodule init
git submodule update
```

Install Smartnic Hardware Build Artifact
----------------------------------------

The firmware build depends on the result of a smartnic hardware (FPGA) build.  This file must be available prior to invoking the firmware build.

This file will be called `artifacts.<board>.<app_name>.0.zip` and should be placed in the `sn-hw` directory in your source tree before starting the firmware build.

Set up your .env file for building a new firmware image
-------------------------------------------------------

The `.env` file tells the build about its inputs and outputs.

There is an `example.env` file in top level directory of this repo that will provide documentation and examples for the values you need to set.

```
cd $(git rev-parse --show-toplevel)
cp example.env .env
```

Since the values in the .env file are used to locate the correct hardware artifact, you will need to (at least) set these values in the `.env` file to match the exact naming of the .zip file you installed in the previous step:
```
SN_HW_BOARD=<board>
SN_HW_APP_NAME=<app_name>
SN_HW_VER=0
```

Build the firmware
------------------

The firmware build creates a docker container with everything needed to interact with your FPGA image.  Without any parameters, the newly built firmware container will be named/tagged `esnet-smartnic-fw:${USER}-dev` and will be available only on the local system.
```
cd $(git rev-parse --show-toplevel)
./build.sh
```

**Optionally** you can use any alternative name by specifying the full container URI as an optional parameter to the build script like this.  Using a fully specified URI here can be useful if you are planning to push the newly built container to a remote docker registry.
```
./build.sh wharf.es.net/ht/esnet-smartnic-fw:${USER}-dev
```

The build script also automatically customizes the `sn-stack/.env` file to refer exactly to the firmware image that you just built.


The entire `sn-stack` directory will need to be transferred to the runtime system.

```
cd $(git rev-parse --show-toplevel)
zip -r artifacts.esnet-smartnic-fw.package.0.zip sn-stack
```

Configuring the firmware runtime environment
--------------------------------------------

**Optionally** The firmware artifact produced by the build (see README.md at the top of this repo) should be transferred to the runtime system that hosts an FPGA card. If your runtime system that hosts an FPGA card is the same one you built the firmware artifacts on (the same one you ran build.sh on), you can skip this step entirely.

```
unzip artifacts.esnet-smartnic-fw.package.0.zip
cd sn-stack
# edit the .env file to provide sane values for
#    FPGA_PCIE_DEV=0000:d8:00
#    COMPOSE_PROFILES=smartnic-mgr-vfio-unlock
# and IFF you have more than one JTAG you also need a line like this
#    HW_TARGET_SERIAL=21760204S029A
```

Verify that the stack configuration is valid

```
docker compose config --quiet && echo "All good!"
```

If this prints anything other than "All good!" then your `.env` configuration file has errors.  Do not proceed until this step passes.

Converting from factory flash image to ESnet Smartnic flash image
-----------------------------------------------------------------

From the factory, the FPGA cards have only a "gold" bitfile in flash with the "user" partition of flash being blank.  The "gold" bitfile has a narrow PCIe memory window for BAR1 and BAR2 which is insufficient for the ESnet Smartnic platform.  Fixing this requires a one-time flash programming step to install an ESnet Smartnic bitfile into the FPGA "user" partition in flash.  This initial setup is done using the JTAG.

Ensure that any running `sn-stack` instances have been stopped so that they don't interfere with the flash programming operation.
```
docker compose down -v --remove-orphans
```

Start the flash rescue service to program an ESnet Smartnic bitfile into the FPGA card "user" partition using the JTAG interface.  This takes approximately 20 minutes.  This process should not be interrupted.
```
docker compose --profile smartnic-flash run --rm smartnic-flash-rescue
```
This will:
* Use JTAG to write a small flash-programing helper bitfile into the FPGA
* Use JTAG to write the current version of the bitfile into the FPGA card's "user" partition in flash
  * Only the "user" partition of the flash is overwritten by this step
  * The "gold" partition is left untouched

Clean up by bringing down the running stack after flash writing has completed.
```
docker compose down -v --remove-orphans
```

**Perform a cold-boot (power cycle) of the server hosting the FPGA card**

It is essential that this is a proper power cycle and not simply a warm reboot.  Specifically **do not** use `shutdown -r now` but rather use something like `ipmitool chassis power cycle`.  Failure to perform a cold-boot here will result in an unusable card.


Normal Operation of the Runtime Environment
===========================================

**NOTE** the steps in this major section are only expected to work once you've completed the initial setup in "One-time Setup of the Runtime Environment".

Running the firmware
--------------------

Start up the full firmware docker stack like this
```
docker compose up -d
```

Verifying the bitfile download
------------------------------

```
docker compose logs smartnic-hw
```

Inspecting registers and interacting with the firmware
------------------------------------------------------

The firmware runtime environment exists inside of the `smartnic-fw` container.  Here, we exec a shell inside of that container and have a look around.

```
docker compose exec smartnic-fw bash
sn-cli dev version
regio syscfg
```

(OPTIONAL) Updating the flash image to a new ESnet Smartnic flash image
-----------------------------------------------------------------------

The instructions in this section are used to **update** the Smartnic flash image **from an already working** Smartnic environment.  This update step is *optional* and only required if you want to change the contents of the FPGA card flash.  Normally, the "RAM" of the FPGA is loaded using JTAG during stack startup.

**NOTE** This will not work for the very first time ever programming the flash.  See "Converting from factory flash image to ESnet Smartnic flash image" section above for first-time setup.

Start up a any properly configured stack which will allow us to write the flash using a fast algorithm over PCIe.

```
docker compose up -d
```

Confirm that PCIe register IO is working in your stack by querying the version registers.

```
docker compose exec smartnic-fw sn-cli dev version
```
Confirm that the "DNA" register is **not** showing 0xfffff... as its contents.

Start the flash update service to write the currently active FPGA bitfile into the persistent flash on the FPGA card.  This takes approximately 7-8 minutes. This process should not be interrupted.
```
docker compose --profile smartnic-flash run --rm smartnic-flash-update
```

Bring down the running stack after flash writing has completed.
```
docker compose down -v --remove-orphans
```

(OPTIONAL) Remove the ESnet Smartnic flash image from the FPGA card to revert to factory image
----------------------------------------------------------------------------------------------

The instructions in this section are used to **remove** the Smartnic flash image **from an already working** Smartnic environment.  This removal step is *optional* and only required if you want to reset the contents of the FPGA card flash back to the factory bitfile.  If you want to keep using the card as an ESnet Smartnic, **do not** perform these operations or you'll have to re-do the  "Converting from factory flash image to ESnet Smartnic flash image" section above.

Start up a any properly configured stack which will allow us to write the flash using a fast algorithm over PCIe.

```
docker compose up -d
```

Confirm that PCIe register IO is working in your stack by querying the version registers.

```
docker compose exec smartnic-fw sn-cli dev version
```
Confirm that the "DNA" register is **not** showing 0xfffff... as its contents.

Start the flash remove service to erase the ESnet Smartnic image from the "user" partition of the FPGA card flash.  This takes less than 1 minute. This process should not be interrupted.
```
docker compose --profile smartnic-flash run --rm smartnic-flash-remove
```

Bring down the running stack after flash reset is completed.
```
docker compose down -v --remove-orphans
```

Using the sn-cli tool
---------------------

The sn-cli tool provides subcommands to help you accomplish many common tasks for inspecting and configuring the smartnic platform components.

All commands described below are expected to be executed within the `smartnic-fw` container environment.  Use this command to enter the appropriate environment.
```
docker compose exec smartnic-fw bash
```

The `sn-cli` tool will automatically look for an environment variable called `SN_CLI_SLOTADDR` which can be set to the PCIe BDF address of the device that you would like to interract with.  In the `smartnic-fw` container, this value will already be set for you.

# Displaying device information with the "dev" subcommand

This will show information about the device such as the build version, build date/time and temperature.

```
root@smartnic-fw:/# sn-cli dev version
Device Version Info
	DNA:           0x40020000012306a21c10c285
	USR_ACCESS:    0x000086d3 (34515)
	BUILD_STATUS:  0x04130920

root@smartnic-fw:/# sn-cli dev temp
Temperature Monitors
	FPGA SLR0:    45.551 (deg C)
```
The `USR_ACCESS` value is typically the unique build pipeline number that produced the embedded FPGA bitfile.
The `BUILD_STATUS` value holds an encoded date/time (Aug 30 at 05:32am) which is when the embedded FPGA bitfile build was started.
The `DNA` value holds the factory programmed unique ID of the FPGA

# Inspecting and Configuring the CMAC (100G) Interfaces with the "cmac" subcommand

Enable/Disable one or more (or all by default) 100G MAC interfaces using these commands:

```
sn-cli cmac enable
sn-cli cmac disable

sn-cli cmac -p 0 enable
sn-cli cmac -p 1 disable
```
Enabling a CMAC interface allows frames to pass (Rx/Tx) at the MAC layer.  These commands **do not affect** whether the underlying physical layer (PHY) is operational.

Display the current MAC and PHY status of one or more (or all by default) 100G MAC interfaces using these commands:
```
root@smartnic-fw:/# sn-cli cmac status
CMAC0
  Tx (MAC ENABLED/PHY UP)
  Rx (MAC ENABLED/PHY UP)

CMAC1
  Tx (MAC ENABLED/PHY UP)
  Rx (MAC ENABLED/PHY DOWN)
```
In the example output above, CMAC0 PHY layer is **UP** in both the Tx and Rx directions.  The MAC is fully enabled.  This link is operational and should be passing packets normally.

In the example output above, CMAC1 PHY layer is **DOWN** in the Rx (receive) direction.  Possible causes for this are:
* No QSFP28 plugged into 100G port 0 the U280 card
* Wrong type of QSFP28 module plugged into 100G port 0
  * 100G QSFP28 SR4 or LR4 modules are supported
  * Some 100G AOC or DACs are known to work
  * QSFP+ 40G modules **are not supported**
  * QSFP 5G modules **are not supported**
* QSFP28 card improperly seated in the U280 card
  * Check if the QSFP28 module is inserted upside down and physically blocked from being fully inserted
  * Unplug/replug the module, ensuring that it is properly oriented and firmly seated
* Fiber not properly inserted
  * Unplug/replug the fiber connection at each end
* Far end is operating in 4x25G or 2x50G split mode
  * The smartnic platform **does not support** 4x25G or 2x50G mode
  * Only 100G mode is supported on each of the U280 100G interfaces
  * Configure far end in 100G mode
* Far end has RS-FEC (Reed-Solomon Forward Error Correction) enabled
  * The smartnic platform **does not support** RS-FEC
  * Disable RS-FEC on the far end equipment

A more detailed status can also be displayed using the `--verbose` option.  Note that the `--verbose` option is a global option and thus must be positioned **before** the `cmac` subcommand.
```
root@smartnic-fw:/# sn-cli --verbose cmac -p 1 status
CMAC1
  Tx (MAC ENABLED/PHY UP)
	           tx_local_fault 0
  Rx (MAC ENABLED/PHY DOWN)
	         rx_got_signal_os 0
	               rx_bad_sfd 0
	          rx_bad_preamble 0
	 rx_test_pattern_mismatch 0
	  rx_received_local_fault 0
	  rx_internal_local_fault 1
	           rx_local_fault 1
	          rx_remote_fault 0
	                rx_hi_ber 0
	           rx_aligned_err 0
	            rx_misaligned 0
	               rx_aligned 0
	                rx_status 0
```

Display summary statistics for packets Rx'd and Tx'd from CMAC ports
```
root@smartnic-fw:/# sn-cli cmac stats
CMAC0: TX      0 RX      0 RX-DISC      0 RX-ERR      0
CMAC1: TX      0 RX      0 RX-DISC      0 RX-ERR      0
```
Note: The CMAC counters are only cleared/reset when the FPGA is reprogrammed.

# Inspecting and Configuring the PCIe Queue DMA (QDMA) block with the "qdma" subcommand

The QDMA block is responsible for managing all DMA queues used for transferring packets and/or events bidirectionally between the U280 card and the Host CPU over the PCIe bus.  In order for any DMA transfers to be allowed on either of the PCIe Physical Functions (PF), an appropriate number of DMA Queue IDs must be provisioned.  This can be done using the `qdma` subcommand.

Configure the number of queues allocated to each of the PCIe Physical Functions
```
sn-cli qdma setqs 1 1
```
This assigns 1 QID to PF0 and 1 QIDs to PF1.  The `setqs` subcommand also takes care of configuring the RSS entropy -> QID map with an equal weighted distribution of all allocated queues.  If you're unsure of how many QIDs to allocate, using `1 1` here is your best choice.

Inspect the configuration of the QDMA block
```
sn-cli qdma status
```

Packet, byte and error counters are tracked for packets heading between the QDMA engine and the user application.  You can display them with this command:
```
sn-cli qdma stats
```
Refer to the `open-nic-shell` documentation for an explanation of exactly where in the FPGA design these statistics are measured.


# Inspecting packet counters in the smartnic platform with the "probe" subcommand

The smartnic platform implements monitoring points in the datapath at various locations.  You an inspect these counters using this command:
```
sn-cli probe stats
```
Refer to the `esnet-smartnic-hw` documentation for an explanation of exactly where in the FPGA design these statistics are measured.

# Configuring the smartnic platform ingress/egress/bypass switch port remapping functions with the "sw" subcommand

The smartnic platform implements reconfigurable ingress and egress port remapping, connections and redirecting.  You can inspect and modify these configuration points using the "sw" subcommand.

Most of the `sw` subcommands take one or more port bindings as parameters.  The port bindings are of the form:
```
<port>:<port-connector>
```
Where:
* `<port>` is one of
  * cmac0  -- 100G port 0
  * cmac1  -- 100G port 1
  * host0  -- DMA over PCIe Physical Function 0 (PF0)
  * host1  -- DMA over PCIe Physical Function 1 (PF1)
* `<port-connector>` is context dependent and is one of
  * cmac0
  * cmac1
  * host0
  * host1
  * bypass -- a high bandwidth channel through the smartnic which does **NOT** pass through the user's application
  * app0   -- user application port 0 (typically a p4 program ingress)
  * app1   -- user application port 1 (only available when user implements it in verilog)
  * drop   -- infinite blackhole that discards all packets sent to it

## Display the current configuration status
```
sn-cli sw status
```

## Remap/rename physical input ports to logical input ports

The `in-port-rename` subcommand allows you to remap the identity of a smartnic platform physical ingress port to any logical port as seen by the user logic.  Once remapped (eg. from `a`->`b`), all following logic in the smartnic will perceive that the packet arrived on ingress port `b` even though it physically arrived on port `a`.  This can be useful for test injection scenarios but would typically be set to a straight-through mapping in production.
```
sn-cli sw in-port-rename a:b
```

To reset this mapping so each port maps to its usual identity:
```
sn-cli sw in-port-rename cmac0:cmac0 cmac1:cmac1 host0:host0 host1:host1
```

## Attach logical input ports to pipelines

The `in-port-connect` subcommand allows you to connect a logical input port to different processing pipelines within the smartnic.  This can be used to connect to a p4 program or to custom logic within the user application.  It can also be used to shunt all packets to a blackhole or to bypass packets around the user application entirely.

```
sn-cli sw in-port-connect cmac0:app0 cmac1:app0 host0:bypass host1:bypass
```

## Connect input ports to output ports in the bypass path

The `bypass-connect` subcommand allows you to connect input ports directly to output ports as they pass through the bypass path (ie. not through the user application).  This is useful for providing direct connectivity from host PCIe PFs to 100G CMAC interfaces for network testing.

```
sn-cli sw bypass-connect host0:cmac0 host1:cmac1 cmac0:host0 cmac1:host1
```

**NOTE** any packets that follow the bypass path will not be processed by the user's p4 program

## Override user application output port decisions and redirect to an alternate port

The `app0-port-redirect` and `app1-port-redirect` subcommands allow the user to override the forwarding decisions made by the user application and/or p4 program and redirect any given output port to a different output port.  This can be useful during development/debugging and in test fixtures.

**NOTE** there are separate overrides for the app0 outputs and the app1 outputs.

```
sn-cli sw app0-port-redirect cmac0:host0 cmac1:host1
sn-cli sw app1-port-redirect cmac0:host0 cmac1:host1
```

To reset this mapping so each output ports maps to its usual destination:
```
sn-cli sw app0-port-redirect cmac0:cmac0 cmac1:cmac1 host0:host0 host1:host1
sn-cli sw app1-port-redirect cmac0:cmac0 cmac1:cmac1 host0:host0 host1:host1
```

Using the sn-p4-cli tool
------------------------

The user's p4 application embedded within the smartnic design may have configurable lookup tables which are used during the wire-speed execution of the packet processing pipeline.  The sn-p4-cli tool provides subcommands to help you to manage the rules in all of the lookup tables defined in your p4 program.

All commands described below are expected to be executed within the `smartnic-fw` container environment.  Use this command to enter the appropriate environment.
```
docker compose exec smartnic-fw bash
```

The `sn-p4-cli` tool will automatically look for an environment variable called `SN_P4_CLI_SERVER` which can be set to the hostname of the `sn-p4-agent` that will perform all of the requested actions on the real hardware.  In the `smartnic-fw` container, this value will already be set for you.

# Inspecting the pipeline structure with the "info" subcommand

The `info` subcommand is used to display the pipeline structure, including table names, match fields (and their types), action names and the list of parameters for each action.  This information can be used to formulate new rule definitions for the other subcommands.

```
sn-p4-cli info
```

# Inserting a new rule into a table

The `table-insert` subcommand allows you to insert a new rule into a specified table.

```
sn-p4-cli table-insert <table-name> <action-name> --match <match-expr> [--param <param-expr>] [--priority <prio-val>]
```
Where:
* `<table-name>` is the name of the table to be operated on
* `<action-name>` is the action that you would like to activate when this rule matches
* `<match-expr>` is one or more match expressions which collectively define when this rule should match a given packet
  * The number and type of the match fields depends on the p4 definition of the table
  * The `--match` option may be specified multiple times and all `match-expr`s will be concatenated
* `<param-expr>` is one or more parameter values which will be returned as a result when this rule matches a given packet
  * The number and type of the action parameters depends on the p4 definition of the action within the table
  * Some actions require zero parameters.  In this case, omit the optional `--param` option entirely.
* `<prio-val>` is the priority to be used to resolve scenarios where multiple matches could occur
  * The `--priority` option is *required* for tables with CAM/TCAM type matches (prefix/range/ternary)
  * The `--priority` option is *prohibited* for tables without CAM/TCAM type mathes

**NOTE**: You can find details about your pipeline structure and valid names by running the `info` subcommand.

# Updating an existing rule within a table

The `table-update` subcommand allows you to update the action and parameters for an existing rule within a table

```
sn-p4-cli table-update <table-name> <new-action-name> --match <match-expr> [--param <new-param-expr>]
```
Where:
* `<table-name>` is the table containing the rule to be updated
* `<new-action-name>` is the new action that should be applied when this rule matches
* `<match-expr>` is the exact original `<match-expr>` used when the original rule was inserted
* `<new-param-expr>` is the set of new parameters to be returned when this rule matches
  * **NOTE**: the new parameters must be consistent with the new action

# Removing previously inserted rules

The `clear-all` and `table-clear` and `table-delete` subcommands allow you to remove rules from tables with varying precision.

Clear all rules from *all tables* in the pipeline.
```
sn-p4-cli clear-all`
```

Clear all rules from a *single* specified table.
```
sn-p4-cli table-clear <table-name>
```

Remove a specific rule from a specific table.
```
table-delete <table-name> --match <match-expr>
```

# Bulk changes of rules using a p4bm simulator rules file

Using the the `p4bm-apply` subcommand, a list of pipeline modifications can be applied from a file.  A subset of the full p4bm simulator file format is supported by the `sn-p4-cli` command.

```
sn-p4-cli p4bm-apply <filename>
```

Supported actions within the p4bm file are:
* `table_insert <table-name> <action-name> <match-expr> => <param-expr> [priority]`
  * Insert a rule
* `clear_all`
  * Clear all rules from all tables
* `table_clear <table-name>`
  * Clear all rules from a specified table

All comment characters `#` and text following them up to the end of the line are ignored.

Stopping the runtime environment
--------------------------------

When we're finished using the smartnic runtime environment, we can stop and remove our docker containers.

```
docker compose down -v
```

Using the smartnic-dpdk container
=================================

The `sn-stack` environment can be started in a mode where the FPGA can be controlled by a DPDK application.  Running in this mode requires a few carefully ordered steps.

Broadly speaking, the steps required to bring up a DPDK application are as follows:
* Bind the `vfio-pci` kernel driver to each FPGA PCIe physical function (PF)
  * This is handled automatically by the sn-stack.
* Run a DPDK application with appropriate DPDK Environment Abstraction Layer (EAL) settings
  * Use `-a $SN_PCIE_DEV.0` to allow control of one or more specific FPGA PCIe PFs
  * Use `-d librte_net_qdma.so` to dynamically link the correct Userspace Polled-Mode Driver (PMD) for the smartnic QDMA engine
  * The EAL will
    * Open the PCIe PFs using the kernel's `vfio-pci` driver
	* Take the FPGA device out of reset
	* Open and map large memory regions for DMA using the kernel's `hugepages` driver
  * The application is responsible for assigning buffers to one or more of the FPGA's DMA queues
* Use the `sn-cli` tool to configure some of the low-level hardware components in the FPGA
  * Configure the set of valid DMA queues in the FPGA (must match what is set in the DPDK application)
  * Bring up the physical ethernet ports

In the examples below, we will be running the `pktgen-dpdk` application to control packet tx/rx via the FPGA's PCIe physical functions.  This can be very useful for injecting packets into a design for testing behaviour on real hardware.

For more information about DPDK in general, see:
* http://core.dpdk.org/doc/

For more information about the `pktgen-dpdk` application, see:
* https://pktgen-dpdk.readthedocs.io/en/latest/index.html

Before you bring up the `sn-stack`, please ensure that you have uncommented this line in your `.env` file
```
COMPOSE_PROFILES=smartnic-dpdk
```

If you changed this while the stack was already running, you'll need to restart the stack with down/up.

First, you'll need to start up the `pktgen` application to open the vfio-pci device for PF0 and PF1 and take the FPGA out of reset.
```
$ docker compose exec smartnic-dpdk bash
root@smartnic-dpdk:/# pktgen -a $SN_PCIE_DEV.0 -a $SN_PCIE_DEV.1 -l 4-8 -n 4 -d librte_net_qdma.so --file-prefix $SN_PCIE_DEV- -- -v -m [5:6].0 -m [7:8].1
Pktgen:/> help
```
NOTE: Leave this application running while doing the remaining setup steps.  The setup steps below must be re-run after each time you restart the pktgen application since the FPGA gets reset between runs.

Open a **separate** shell window which you will use for doing the low-level smartnic platform configuration.

Configure the Queue mappings for host PF0 and PF1 interfaces and bring up the physical ethernet ports using the `smartnic-fw` container.

```
$ docker compose exec smartnic-fw bash
root@smartnic-fw:/# sn-cli qdma setqs 1 1
root@smartnic-fw:/# sn-cli qdma status
root@smartnic-fw:/# sn-cli cmac enable
root@smartnic-fw:/# sn-cli cmac status
```
Setting up the queue mappings tells the smartnic platform which QDMA queues to use for h2c and c2h packets.  Enabling the CMACs allows Rx and Tx packets to flow (look for `MAC ENABLED/PHY UP`).


## Advanced Lesson 7: DPDK

In this lesson, you will follow simple steps that allow you to use DPDK and pktgen to interact with the P4 program that you have loaded onto the FPGA.

Advanced usage of the pktgen-dpdk application
=============================================

Example of streaming packets out of an interface from a pcap file rather than generating the packets within the UI.
Note the `-s <P>:file.pcap` option where `P` refers to the port number to bind the pcap file to.

```
root@smartnic-dpdk:/# pktgen -a $SN_PCIE_DEV.0 -a $SN_PCIE_DEV.1 -l 4-8 -n 4 -d librte_net_qdma.so --file-prefix $SN_PCIE_DEV- -- -v -m [5:6].0 -m [7:8].1 -s 1:your_custom.pcap
Pktgen:/> port 1
Pktgen:/> page pcap
Pktgen:/> page main
Pktgen:/> start 1
Pktgen:/> stop 1
Pktgen:/> clr
```

Example of running a particular test case via a script rather than typing at the UI

```
cat <<_EOF > /tmp/test.pkt
clr
set 1 size 1400
set 1 count 1000000
enable 0 capture
start 1
disable 0 capture
_EOF
```

```
root@smartnic-dpdk:/# pktgen -a $SN_PCIE_DEV.0 -a SN_PCIE_DEV.1 -l 4-8 -n 4 -d librte_net_qdma.so --file-prefix $SN_PCIE_DEV- -- -v -m [5:6].0 -m [7:8].1 -f /tmp/test.pkt
```