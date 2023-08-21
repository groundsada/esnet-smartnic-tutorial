# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

## Lesson 1: Getting Started

Welcome to the tutorial on utilizing Xilinx Alveo FPGAs as SmartNICs within the ESnet framework. In this tutorial, we'll guide you through the process of setting up, configuring, and utilizing the power of Xilinx Alveo FPGAs for network acceleration using the ESnet framework.

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

## Lesson 2: esnet-smartnic-hw

Link: **[Lesson 2: esnet-smartnic-hw](2-lesson2.md)**

