# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

## **Operator's Guide to Configuring an Alveo Host**

This guide provides an in-depth walkthrough of the process involved in harnessing the capabilities of Xilinx Alveo FPGAs as SmartNICs within the ESnet Framework. Divided into two core sections, the guide outlines the steps required for both development and deployment phases.

---

### **Development Phase**

The development phase is a critical initial step, encompassing the setup of a conducive environment for crafting, testing, and compiling P4 programs. These programs will eventually be transformed into executable artifacts capable of running on Xilinx Alveo FPGAs.

**1. Installation and Configuration of the Vivado Runtime Environment**

To commence, it is imperative to install the AMD (Xilinx) Vivado tool suite, incorporating the VitisNetP4 option. This entails setting the `VitisNetP4_Option_VISIBLE` environment variable to `true` before initiating the installation process. Execute the following BASH shell command:

```export VitisNetP4_Option_VISIBLE=true```

**2. Configuring the Runtime Environment**

Execute the `settings64.sh` script located in the Vivado installation directory to configure the runtime environment effectively:

```source /tools/Xilinx/Vivado/2023.1/settings64.sh```

Remember, in this illustration, the Vivado installation directory is situated at `/tools/Xilinx/Vivado/2023.1/`.

**3. Resolving License Dependencies**

The `XILINXD_LICENSE_FILE` environment variable needs proper configuration to facilitate license resolution for the AMD (Xilinx) VitisNetp4 IP core. This can be achieved using a `.flexlmrc` file in the user's home directory or a dedicated BASH script file (like `.bashrc`). Execute the following BASH shell command as an example:

```export XILINXD_LICENSE_FILE=<filename>```

Once these steps are completed and you have a valid VitisNetworkingP4 license, your environment will be primed for the development of P4 programs tailored for Xilinx Alveo FPGA cards.

---

### **Deployment Phase**

The deployment phase entails transferring the compiled bitfiles onto Xilinx Alveo FPGAs. To accomplish this, follow these sequential steps:

**1. Ensuring Compatibility and Environment**

Ensure that your system operates within a Linux environment, ideally Ubuntu 20.04, for a stable and reproducible deployment.

**2. FPGA Connectivity Check**

Validate that your host system is equipped with at least one Xilinx FPGA (U280, U55C, U250). Confirm this by running the command:

```lspci -d 10ee```

This command will display any PCIe-connected FPGAs.

**3. Configuring Hugepages**

Verify that hugepages are appropriately configured by examining the kernel boot command line parameters:

```cat /proc/cmdline```

Ensure that the output includes lines similar to:

```
BOOT_IMAGE=/boot/vmlinuz-5.4.0-126-generic root=/dev/mapper/vg0-root ro default_hugepagesz=1G hugepagesz=1G hugepages=32 intel_iommu=on iommu=pt
```

**4. Grub Configuration**

Edit the `/etc/default/grub` file to include the following line:

```GRUB_CMDLINE_LINUX_DEFAULT="default_hugepagesz=1G hugepagesz=1G hugepages=32 intel_iommu=on iommu=pt"```

Subsequently, execute:

```sudo update-grub```

**5. IOMMU Confirmation**

Verify IOMMU is enabled by examining the log:

```sudo less /var/log/kern.log```

**6. JTAG/USB Connection Check**

Ensure that the FPGA has JTAG/USB connectivity by running:

```lsusb```

Confirm the presence of the device labeled "Future Technology Devices International, Ltd FT232H Single HS USB-UART/FIFO," which signifies the JTAG connection to the FPGA.

**7. Docker Setup**

Install Docker along with the `docker-compose-plugin` and `docker-buildx-plugin`. Detailed instructions can be found in <a href="https://docs.docker.com/engine/install/ubuntu/">Docker's official documentation</a>.

**8. Post-Installation Configuration**

Follow the post-installation steps outlined in <a href="https://docs.docker.com/engine/install/linux-postinstall/">Docker's documentation</a> to grant non-privileged users the ability to execute Docker commands and to implement optional configurations.

---

## **Completion and References**

With the successful completion of these phases, your setup is now ready for operation. Should you need further information, the following documents are accessible from the AMD (Xilinx) Vitis Networking P4 Secure Site (upon granted access):

- *Vitis Networking P4 Installation Guide and Release Notes, UG1307 (v2023.1) May 10, 2023*.
- *Vitis Networking P4 User Guide, UG1308 (v2023.1) May 16, 2023*.
- *Vitis Networking P4 Getting Started Guide, UG1373 (v2023.1) May 16, 2023*.

Additionally, you may find valuable resources at <a href="https://p4.org/">https://p4.org/</a>.

## **Known Issues**

As of now, no known issues have been reported. Your experience should be seamless.