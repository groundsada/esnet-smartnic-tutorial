# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

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

## Advanced Lesson 6: esnet-smartnic-fw

Link: **[Advanced Lesson 6: esnet-smartnic-fw](6-lesson6.md)**

