# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

## Lesson 3: The Deployment Workflow

In this lesson, we will explore the deployment workflow, which involves the deployment of compiled bitfiles (zipped artifacts) onto the FPGA card. This stage results in a stack of three essential Docker images, namely: 

1. **esnet-smartnic-fw**: This Docker image is crucial for your specific bitfiles (artifacts). You will need to rebuild it each time you modify the bitfiles. It's important to note that the rebuilding process becomes significantly faster after the first time.

2. **smartnic-dpdk-docker**: This image is bitfile-independent and only needs to be built once. It serves as an essential component for interacting with the FPGA card. You can tar this image and transfer it to your runtime environment, provided that the host system where it was created matches the CPU architecture of your runtime environment. This Docker image provides the DPDK and pktgen tools.

3. **xilinx-labtools-docker**: Similar to the smartnic-dpdk-docker image, this Docker image is bitfile-independent and needs to be constructed only once. It complements the functionality of the FPGA card and is transferable to your runtime environment, given the aforementioned CPU architecture compatibility. This Docker image gives you access to Vivado Lab (no license needed). Vivado Lab is the software you'll use to load new programs onto your FPGA, so it's a crucial part of the setup.

It's crucial to ensure that all three Docker images, along with the corresponding configuration files, are present to effectively utilize the ESnet framework on an FPGA card. This deployment process can be replicated on multiple hosts with FPGAs as well, as long as the three containers and the sn-stack/ folder are available on the new host.

Once the deployment is complete, you gain access to a powerful set of tools for your experiments:

- **DPDK (Data Plane Development Kit)**: DPDK is an application that enables you to bypass the kernel of the slice and connect directly to the FPGA. This allows for high-performance packet processing and efficient data plane operations.

- **Pktgen**: Pktgen is a DPDK application that facilitates the transmission of packets to and from the FPGA SmartNIC through its 2x100 Gbps ports. It also enables communication with the slice host using the PCIe bus. Pktgen is a valuable tool for testing and analyzing network performance.

- **ESnet CLI Tools**: These command-line tools provide control over the QDMA queues, access to probe counters for packet statistics, management of the control plane rules for your P4 logic, and remapping of egress ports to suit the requirements of your specific experiment.


### Setting up the build environment

The smartnic firmware build depends on `docker` and the `docker compose` plugin.

#### Docker

Install Docker on your system following the instructions found here for the **linux** variant that you are using
* https://docs.docker.com/engine/install/

Ensure that you follow the post-install instructions here so that you can run docker **without sudo**
* https://docs.docker.com/engine/install/linux-postinstall/

Verify your docker setup by running this as an ordinary (non-root) user without using `sudo`
```
docker run hello-world
```

### Docker Compose

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

#### Git Submodules

Ensure that all submodules have been pulled.

```
git submodule init
git submodule update
```

### Building a new firmware image


#### Install Smartnic Hardware Build Artifact

The firmware build depends on the result of a smartnic hardware (FPGA) build.  This file must be available prior to invoking the firmware build.

This file will be called `artifacts.<board>.<app_name>.0.zip` and should be placed in the `sn-hw` directory in your source tree before starting the firmware build.


#### Create 'smartnic-dpdk-docker' Image

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

- You should see an image called `smartnic-dpdk-docker` with tag `${USER}-dev`.

Alternatively, you can tar this image and transfer it to your runtime environment, provided that the host system where it was created matches the CPU architecture of the environment.


#### Create 'xilinx-labtools-docker' Image

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

Alternatively, you can tar this image and transfer it to your runtime environment, provided that the host system where it was created matches the CPU architecture of the environment.

### Building a new firmware image


#### Clone the esnet-smartnic-fw repository

`git clone https://github.com/esnet/esnet-smartnic-fw.git`

#### Git Submodules

Ensure that all submodules have been pulled.

```
git submodule init
git submodule update
```

#### Install Smartnic Hardware Build Artifact

The firmware build depends on the result of a smartnic hardware (FPGA) build.  This file must be available prior to invoking the firmware build.

This file will be called `artifacts.<board>.<app_name>.0.zip` and should be placed in the `sn-hw` directory in your source tree before starting the firmware build.

#### Set up your .env file for building a new firmware image

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

#### Build the firmware

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

#### Configuring the firmware runtime environment

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

## Lesson 4: Running the Deployment

Link: **[Lesson 4: Running the Deployment](4-lesson4.md)**

