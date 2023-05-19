# Vagrant
Simplifies managing VMs for development and integration
- stand up one or more VMs
- configures networking

## Terms
Base Box - combo of OS (e.g. RHEL 8) and hypervisor
Providers - hypervisors...VirtualBox, VMWare, Hyper-V (on Windows)
Provisioners - shell, Ansible, Puppet, Chef

## Configuration
Managed inside **Vagrantfile**

## Commands
vagrant up --> reads from Vagrantfile
vagrant halt
vagrant destroy
