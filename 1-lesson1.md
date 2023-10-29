# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

## Lesson 1: Overview

The ESnet SmartNIC framework provides an entire workflow to program AMD/Xilinx Alveo FPGA cards using P4. The ESnet framework is open-source and available on GitHub. ESnet is a high-performance network that supports scientific research. The ESnet team created the framework that seamlessly integrates AMD/Xilinx tools along with various tools like DPDK to provide an easy way of programming Alveo cards as SmartNICs.

To program Alveo cards with P4 and deploy your applications effectively, we will divide the process into two main workflows:
Development Workflow: This phase involves compiling your P4 programs into bitfiles. It necessitates the use of licensed tools from Xilinx and access to an FPGA. Experimenters have the option to either join the XUP (Xilinx/AMD University Program) to request the required tool licenses and FPGA hardware or utilize one of the available facilities that offer access, which can be found in the following reference (provided by the good folks of FABRIC):

Access to FPGA Resources (https://learn.fabric-testbed.net/knowledge-base/using-xilinx-u280-fpgas-on-fabric/)

Deployment Workflow: This stage requires Alveo cards, enabling you to load your generated bitfiles onto the cards for experimental purposes.
By following these two workflows, you can efficiently program and deploy Alveo FPGA cards as SmartNICs, making them integral to your high-performance network infrastructure.

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

## Lesson 2: The Development Workflow

Link: **[Lesson 2: The Development Workflow](2-lesson2.md)**
