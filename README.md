# Project JPxConf ![GitHub version](https://img.shields.io/github/release/jplfp/JPxConf.svg)

This project aims to create an ansible based tool for auto provisioning and deploying of VMs/Baremetals on different environments:

* Openstack
* ESXi
* KVM

<br/>
<br/>



## Contents
- [Features](#features)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Contributing](#contributing)
- [Authors](#authors)
- [Code of Conduct](#code-of-conduct)
- [License](#license)

<br/>
<br/>



## Features

### Provisioning
#### Basic
- Inject SSH Keys
- Set the hostname
#### Security
- Deactivate firewalld service
- Disable selinux
#### Networking
- Deactivate NetworkManager service
- Enable ipv4 forwarding
- Set the DNS servers
- Set the proxy server
#### Repositories
- Add EPEL repository
- Add custom repository

### Deploy
#### Pkginstall
- Install selected minimal packages
- Install additional full packages
#### Pkgconfigure
- Configure previous installed packages if needed

<br/>
<br/>



## Getting Started
To run this project you have to fullfil some prerequisites and follow the installation guide below.

### Prerequisites

- An ansible enabled machine with version > 2.7 (this check is enforced at the start of the playbook)

```
ansible --version
ansible 2.7.7
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.5 (default, Jul 13 2018, 13:06:57) [GCC 4.8.5 20150623 (Red Hat 4.8.5-28)]
```
If you need to install ansible just run `yum install ansible` on a Centos/RHEL system or `apt-get install ansible` on a Debian system
<br/>
<br/>

- git package installed on the same machine
```
git --version
git version 1.8.3.1
```
If you need to install git just run `yum install git` on a Centos/RHEL system or `apt-get install git` on a Debian system
<br/>
<br/>


- A set of hosts on which you want to run the tool with the following:
  - root user access with password
  - Centos or Redhat Operating Systems (For now only version 7.5 was tested)
<br/>
<br/>

### Installation

- Clone the repo to your local machine:
```
git clone https://github.com/jplfp/JPxConf.git
```

<br/>
<br/>


## Usage

To use this tool you must define first some parameters related to the hosts and to the tool itself as instructed below.

### Create a new hosts file
- Create a new hosts file which contains the hosts you want to run the tool on and save it under `inventory/hosts`

Hosts File Example:
```
# Provisioning role
[common]
HOST1HOSTNAME ansible_host=HOST1IP ansible_password=HOST1PASSWORD
HOST2HOSTNAME ansible_host=HOST2IP ansible_password=HOST2PASSWORD

# Deploy role
[minimal:children]
full
HOST1HOSTNAME

[full]
HOST1HOSTNAME
```

As you can see we have multiple groups defined each one with its function:
- The `common` group contains the hosts configuration and the tool will apply the [provisioning role tasks](#provisioning) on them (unless otherwise configured on the variables file below)
- The `minimal` group contains the hosts on which you want to deploy the [minimal packages](#pkginstall) (which are defined on the variables file below)
- The `full` group contains the hosts on which you want to deploy the [full packages](#pkginstall) (which are defined on the variables file below)

<br/>

### Edit the existing variables file
- Edit the existing variables file under `inventory/group_vars/all` which contains the specific parameters and variables for your hosts and services

Variables File Example:
```yaml
---
#####################################
####  Variables for Common Role  ####
#####################################

###  basic.yml  ###
# Adding SSH Key from the local machine to the hosts #
inject_ssh_key: true
ssh_local_key_path: "~/.ssh/id_rsa.pub"


###  security.yml  ###
# Stopping and disabling the firewalld service #
disable_firewalld: true

# Setting selinux to disabled state #
disable_selinux: true


###  networking.yml  ###
# Enabling ipv4 forwarding #
enable_ipv4_forwarding: true

# Setting the DNS Servers #
dns_servers:
  - DNSSERVERIP1
  - DNSSERVERIP2

# Setting the Proxy Server #
enable_proxy: true
proxy_server: http://PROXYSERVERIP:PORT


###  repositories.yml  ###
enable_rhel_custom_repo: true
custom_base_url: http://REPOSITORYSERVERIP:PORT/PATH
custom_repo_osp_version: 14



#####################################
####  Variables for Deploy Role  ####
#####################################

###  pkginstall.yml  ###
# Installing selected packages #
min_pkgs_to_install:
  - screen
  - ftp
  - wget
  - ntp
  - nc
  - iperf3
  - tcpdump
  - nmap
  - httpd
  - net-tools
  - telnet

# Installing additional full packages #
full_pkgs_to_install:
  - libvirt
  - libvirt-python
  - libvirt-client
  - libguestfs-tools
  - virt-install
  - virt-viewer
  - virt-manager
  - qemu-kvm
  - qemu-img
  - bridge-utils 


###  pkgconfigure.yml  ###
# Copy over the NTP configuration #
ntp_servers:
  - NTPSERVERIP1
  - NTPSERVERIP2
```
The file is pretty self-explanatory. You can enable or disable (set to true/false) some of the actions to be performed.

<br/>

### Run the playbook
- Now you just need to run the main playbook (`site.yml`) with:
```
ansible-playbook -i inventory/hosts site.yml
```
- You can also run just a specific role using for example tags:
```
ansible-playbook -i inventory/hosts site.yml --tags=provisioning
OR
ansible-playbook -i inventory/hosts site.yml --tags=deploy
```

<br/>
<br/>



## Contributing

This was a project born for personal use so some of the features you need probably aren't here yet. I will keep on trying to add new features and if you want to help out, contribute with ideas or tag issues please do it! :thumbsup:
For more details check the [contributing file](CONTRIBUTING.md)

<br/>
<br/>



## Authors

- João Feteira - [Github](https://github.com/jplfp)
- [contributors](https://github.com/jplfp/JPxConf/graphs/contributors) who participated in this project.

<br/>
<br/>


## Code of Conduct
Check the [code of conduct](CODE_OF_CONDUCT.md)

<br/>
<br/>

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
