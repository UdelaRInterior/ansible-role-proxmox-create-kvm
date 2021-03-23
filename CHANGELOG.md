# Change Log

## [v3.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm/tree/v3.0.0)

* End of v1.0.0 variables' API backward's compatibility, no longer needed and not considered clean code

## [v2.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm/tree/v2.0.0)

* New role's variables interface, with a uniform namespace pve_kvm_*
* Now possible to define the proxmox hostname as a variable, `pve_kvm_hostname`

## [v1.2.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm/tree/v1.2.0)

* Added variable to parameterize proxmox_kvm module timeout

## [v1.1.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm/tree/v1.1.0)

* Added the possibility to turn on the VM after create it

## [v1.0.1](https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm/tree/v1.0.1)

* Added `clone_target`, `clone_vmid` and `clone_newid` variables to fully support cloning VMs. See [#4](https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm/pull/4) [#6](https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm/pull/6)


## [v1.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm/tree/v1.0.0)

* First stable version. VM creation from scratch with multiple storage units and network interfaces or cloning from an existing template