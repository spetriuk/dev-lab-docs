# OS setup 

> Installing OS on bare metal over the network

The OS installation playbook has been tested with Ubuntu 20.04. 
If you want to install Debian or Fedora, you should change [cloud-init]({{repo.url}}/os/install/pxe/files/cloud-init) files to match the OS requirements.
The easy way to get cloud-init config files is to copy it from the working machine. 
For the Ubuntu 20.04 distribution it is located by `/var/log/installer/autoinstall-user-data`

## Prerequisites

### Initial controller

> The initial controller is the machine used to bootstrap the servers, we only need it once, you can use your laptop or desktop

A Linux machine that can run Docker (because the host networking driver used for PXE boot only supports Linux, you can use a Linux virtual machine with bridged networking if you're on macOS or Windows).

The following ports should be free and not blocked by firewall:

| service | port    | protocol |
|---------|---------|----------|
| DHCP    | 67      | UDP      |
| TFTP    | 69      | UDP      |
| HTTP    | 80      | TCP      |
| DNS     | 53      | TCP/UDP  |

#### Tools

- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).
  Also some basic collections required: `community.general` and `community.docker`
- [Docker](https://docs.docker.com/engine/install/) and [docker-compose](https://docs.docker.com/compose/install/)

### Server machines 

Any modern x86_64 computer(s) should work, you can use old PCs, laptops or servers, but they should support:
  - PXE boot - ability to boot from the network
  - Wake-on-LAN capability - used to wake the machines up automatically without physically touching the power button

### Variables

Vars should be defined in [vars/main.yaml]({{repo.url}}/os/install/vars/main.yaml) file:

| variable     | description                               | notes                                                                                        |
|--------------|-------------------------------------------|----------------------------------------------------------------------------------------------|
| os_image_url | Direct url of OS live-server distribution | Ubuntu daily live iso is [here](http://cdimage.ubuntu.com/ubuntu-server/daily-live/current/) |
| os_username  | Username of the user to be created        | The same user will be created on all machines                                                |
| os_password  | User's password                           | Should be encrypted                                                                          |
|              |                                           |                                                                                              |

Hosts information should be defined in [hosts]({{repo.url}}/os/install/hosts) file. Before install you should collect the following information about your server machines:
  - MAC address
  - Disk name (for example /dev/sda)
  - Network interface name (for example eno1)
  - Choose a desired static IP address for each machine 
  
## Deployment

OS installation is going to be performed via network using PXE boot. 
PXE is a client-server interface that allows computers in a network to be booted from the server before deploying the obtained OS image.

The OS deployment workflow via PXE will look like this:

![](./assets/pxe-sequence.png)

We will use `dnsmasq` for DHCP + TFTP servers and `nginx` for HTTP server.

The `dnsmasq` docker image based on [@axtstar](https://github.com/axtstar)'s [**Dockerfile**](https://github.com/axtstar/pxeboot_ubuntu_20_04/blob/master/Dockerfile)

