# Using Xilinx Alveo FPGAs as SmartNICs with ESnet Framework

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

## Conclusion

Congratulations! You've successfully set up Xilinx Alveo FPGAs as SmartNICs using the ESnet framework. This powerful combination opens doors to accelerated network processing and customization. Feel free to explore further, experiment, and innovate with FPGA-based SmartNIC solutions.

For more information, visit the official ESnet repositories:
- [esnet-smartnic-hw](https://github.com/esnet/esnet-smartnic-hw)
- [esnet-smartnic-fw](https://github.com/esnet/esnet-smartnic-fw)
- [smartnic-dpdk-docker](https://github.com/esnet/smartnic-dpdk-docker)
- [xilinx-labtools-docker](https://github.com/esnet/xilinx-labtools-docker)

Happy networking!