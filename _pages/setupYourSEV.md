---
layout: page
title: Set UP SEV VMs
permalink: /setupYourSEV
comments: true
---

# Play with SEV VM

### How to set up your first SEV VM.

Using the following check list to set up your own SEV VM (Ubuntu version).

- [ ]  **Determine if your machine supports SEV.**

    Usually, CPUs from the AMD EPYC series should all support SEV. We can also check SEV support by looking at *CPUID 8000_001F[EAX]*. Bit 1 indicates support for SEV. The maximum number of active SEV VMs supported at the same time can be determined by CPUID *0x8000001f[ECX]* (e.g., 15, which suggests the 15 SEV VMs can run at the same time). 

- [ ]  **Prepare Host OS.**

    We then need to make sure the host OS is sufficient  for launching SEV-enabled VMs. One should make sure:

    - Kernel ≥ 4.16
    - Libvirt ≥ 4.5
    - QEMU ≥ 2.12
    - OVMF ≥ commit (75b7aa9528bd)

    We can install the newest QEMU & OVMF & kernel from the [**AMDSEV repo](https://github.com/AMDESE/AMDSEV)** by using the following commands, which will install necessary dependencies, compile and install the newest SEV-related commits.  

    ```bash
    git clone [https://github.com/AMDESE/AMDSEV.git](https://github.com/AMDESE/AMDSEV.git)
    cd AMDSEV/distros/ubuntu-18.04
    ./build.sh
    ```

    For those components, QEMU is used to emulate peripheral hardware devices; OVMF is used to boot the SEV VM; an updated host kernel is used to manage SEV-related APIs and maintain necessary structures used to host SEV VMs (e.g., [nested page table](https://www.notion.so/Nested-Page-Table-8575da3d33844323af07467f34a06887)).  

- [ ]  **Prepare SEV VM's image.**

    After preparing the host OS, we need to prepare the VM's image. We can use the following commands to generate an empty local image with 30GB capability. 

    ```bash
    qemu-img create -f qcow2 image-name.qcow2 30G
    ```

    We then copy a per-VM specific boot file (OVMF_VARS.fd) along with our VM image. 

    ```bash
    cp /usr/local/share/qemu/OVMF_VARS.fd OVMF_VARS.fd
    ```

    Then we can boot the VM with an Ubuntu image (ubuntu-xxxx-xxxx-amd64.iso). A kernel version larger than 4.16 is recommended. 

    ```bash
    [launch-qemu.sh](http://launch-qemu.sh/) -hda image-name.qcow2 -cdrom ubuntu-xxxx-xxxx-amd64.iso
    ```

- [ ]  **Check if SEV is enabled in your VM.**

    After successfully install your SEV VM, just lookup at the system dmsg to check if SEV is enabled or not. Note that how to enable a SEV-ES enabled VM is a little different from setting up a SEV-enabled VM, which requires higher software version, which can be found in the [AMD SEV-ES branch](https://github.com/AMDESE/AMDSEV/tree/sev-es). 

    ![play_1]({{ site.baseurl }}/assets/images/play_1.png)![S

### Try SEV-enabled VM with other host OS.

AMD also lists the tutorials to set up SEV-enabled VMs with other versions (e.g., [SLES-15](https://github.com/AMDESE/AMDSEV#sles-15), [RHEL-8](https://github.com/AMDESE/AMDSEV#rhel-8), [Fedora-28](https://github.com/AMDESE/AMDSEV#fc-28), [Fedora-29](https://github.com/AMDESE/AMDSEV#fc-29), [openSuse-Tumbleweed](https://github.com/AMDESE/AMDSEV#tumbleweed) ) in its [**AMDSEV repo**.](https://github.com/AMDESE/AMDSEV)