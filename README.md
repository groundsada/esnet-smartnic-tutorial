# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Copyright Notice](#copyright-notice)

- [Lesson 1: Getting Started](1-lesson1.md)
- [Lesson 2: esnet-smartnic-hw](2-lesson2.md)
- [Lesson 3: P4 Simulation and Testing](3-lesson3.md)
- [Lesson 4: The ESnet stack](4-lesson4.md)
- [Lesson 5: esnet-smartnic-fw](5-lesson5.md)
- [Lesson 6: DPDK](6-lesson6.md)
- [Appendix A: Operator's Guide](z-appendix_a.md)

---

## Introduction

Welcome to the tutorial on utilizing Xilinx Alveo FPGAs as SmartNICs within the ESnet framework. In this tutorial, we'll guide you through the process of setting up, configuring, and utilizing the power of Xilinx Alveo FPGAs for network acceleration using the ESnet framework.

## Prerequisites

Before you begin, ensure you have the following:

- Xilinx Alveo FPGA card (compatible model list [here](https://www.xilinx.com/products/boards-and-kits/alveo.html))
- Host system with compatible hardware and Linux environment
- Basic understanding of FPGA programming and networking concepts

## Getting Started

Let's get started with the setup process:

1. [Setting Up Xilinx Alveo](#setting-up-xilinx-alveo)
2. [Installing ESnet Framework](#installing-esnet-framework)
3. [Configuring SmartNIC Functionality](#configuring-smartnic-functionality)
4. [Exploring Advanced Features](#advanced-features)

## Setting Up Xilinx Alveo

Follow these steps to set up your Xilinx Alveo FPGA card:

1. Insert the Alveo card into a compatible PCIe slot on your host system.
2. Connect necessary power cables and ensure proper power supply.
3. Install Xilinx Vitis development tools from [Xilinx's official website](https://www.xilinx.com/support/download.html).

## Installing ESnet Framework

To install the ESnet framework, follow these steps:

1. Open a terminal and navigate to your preferred installation directory.
2. Clone the ESnet repository:
    ```bash
    git clone https://github.com/ESnet/ESnet-codebase.git
    ```
3. Install required dependencies:
    ```bash
    cd ESnet-codebase
    pip install -r requirements.txt
    ```

## Configuring SmartNIC Functionality

Configure the Xilinx Alveo FPGA as a SmartNIC using the ESnet framework:

1. Navigate to the ESnet-codebase directory.
2. Run the SmartNIC configuration script:
    ```bash
    python configure_smartnic.py --interface eth0 --fpga /dev/fpga0
    ```

## Advanced Features

Explore advanced features and customization options:

1. **Packet Filtering:** Modify the packet filtering rules in the configuration script to tailor network traffic processing.
2. **Custom FPGA Designs:** Integrate your custom FPGA designs for specialized network functions.
3. **Performance Tuning:** Adjust parameters in the ESnet configuration for optimal SmartNIC performance.

Continue your journey by delving into these advanced capabilities.

## Troubleshooting

Encountering issues? Here are a few tips:

- Ensure all hardware connections are secure.
- Double-check software installations and versions.
- Refer to the ESnet framework documentation for detailed troubleshooting steps.

## Conclusion

Congratulations! You've successfully set up Xilinx Alveo FPGAs as SmartNICs using the ESnet framework. This powerful combination opens doors to accelerated network processing and customization. Feel free to explore further, experiment, and innovate with FPGA-based SmartNIC solutions.

For more information, visit the official [Xilinx Alveo website](https://www.xilinx.com/products/boards-and-kits/alveo.html) and the [ESnet framework documentation](https://esnet-pyroute.readthedocs.io/en/latest/).

Happy networking!

---

This concludes our tutorial on utilizing Xilinx Alveo FPGAs as SmartNICs with the ESnet framework. We hope you found this guide informative and inspiring for your FPGA-accelerated networking projects.

-->