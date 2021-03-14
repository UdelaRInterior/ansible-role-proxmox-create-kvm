Ansible role proxmox_create_kvm
=========

 A complete role for KVM virtual machines creation in a Proxmox Virtual Environement (PVE) cluster, with multiple network interfaces and storage units.

Requirements
------------

You must act on a Proxmox node or cluster already configured, i.e. you need Proxmox Virtual Environement (PVE) node already installed (tested with PVE 5), and a Proxmox user with VM creation rights.


Installation
------------

To be able to update later and eventually to modify it, prefer using `requirements.yml` with the git source:

```yaml
- name: create_kmv
  src: https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm.git
  ```
And then download it with `ansible-galaxy`:

```shell
$ ansible-galaxy install -r requirements.yml
```

Using `git`, you'll have to be carefull to folder name :

```shell
$ git clone https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm.git create_kvm
```

Role Variables
--------------

The `defaults` variables define the VM parameters. To be specified by host under `host_vars/host_fqdn/vars` and eventually encrypted in `host_vars/host_fqdn/vault`

New interface in v2.0.0, with all role variables defined in the `pve_kvm_*` namespace. Update your host variables in your ansible code!

To give time to update your whole inventory, the role preserves backward compatibility with [previous interface](https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm/blob/v1.2.0/README.md#role-variables) up to v3.X.Y release. 


```yaml
pve_kvm_node: my_node
pve_kvm_api_host: my_node.my_cluster.org
pve_kvm_api_user: deploy@pam
pve_kvm_api_password: "pass1234" ## Better put passwords in a vault, ex. [group_vars | host_vars]/{{ inventory_hostname }}/vault/main.yml
pve_kvm_node_deploy_password: D3pl0y_pwd

pve_kvm_start_after_create: false # Turn on the VM once created. Whether created from scratch or cloned
pve_kvm_timeout: 500 # Timeout (in seconds) for operations, both clone and create

# If the VM is cloned, the other parameters are not used, the assigned hardware characteristics are inherited from the cloned VM
pve_kvm_clone_from_existing: false        # FALSE: create a new VM  -  TRUE: clone an existing VM
pve_kvm_full_clone: true                  # FALSE: linked clone  -  TRUE: full clone ## See https://github.com/UdelaRInterior/ansible-role-proxmox-create-kvm/issues/2
pve_kvm_clone_vm: DebianBusterTemplate    # The VM to clone
pve_kvm_clone_storage: local-lvm          # Target storage for full clone.
pve_kvm_clone_format: raw                 # Target drive's backing file's data format. Used only with clone
                                          # Choices: cloop - cow - qcow - qcow2 (default) - qed - raw - vmdk
pve_kvm_clone_target: "{{ pve_kvm_node }}"        # Target node
# pve_kvm_clone_vmid: 1                   # VM source id
# pve_kvm_clone_newid: 9                  # Clone id
                                  # Choices: cloop - cow - qcow - qcow2 (default) - qed - raw - vmdk

pve_kvm_description: LAMP Server
pve_kvm_kvm: yes
pve_kvm_ostype: l26   # Choices: l24 - l26 (default) - other - wxp - w2k - w2k3 - w2k8 - wvista - win7 - win8 - solaris

pve_kvm_sockets: 1
pve_kvm_cores: 1
pve_kvm_cpuunits: 1000
pve_kvm_cpulimit: 0
pve_kvm_memory: 512
pve_kvm_balloon: 0
pve_kvm_shares: 0     # Amount of memory shares for auto-ballooning. (0 - 50000). Using 0 disables auto-ballooning, this means no limit.
pve_kvm_vga: std      # Select VGA type. If you want to use high resolution modes (>= 1280x1024x16) then you should use option 'std' or 'vmware'.
              # Choices: std (default) - cirrus - vmware - qxl - serial0 - serial1 - serial2 - serial3 - qxl2 - qxl3 - qxl4

pve_kvm_hard_disks:
  - id: 0                         #  0 ≤ n ≤ 15
    bus: virtio                   # Optional. Available buses: virtio (default) - ide - sata - scsi
    storage: local-lvm
    size: 32
    format: raw                   # Optional. Available formats: qcow2 - raw (default) - subvol
    backup: true                  # Optional
    skip_replication: false       # Optional
    cache: none                   # Optional. Available types: none (default) - directsync - writethrough - writeback - unsafe
    io_thread: false              # Optional
    ssd_emulation: false          # Optional

  - id: 1
    bus: sata
    storage: local-lvm
    size: 16
    ssd_emulation: true
    backup: false

pve_kvm_net_interfaces:
  - id: net0
    model: virtio                 # Optional. Available models: virtio - e1000 - rtl8139 - vmxnet3
    hwaddr: 46:70:B7:26:87:4F     # Optional. If not indicated, Proxmox will assign one automatically.
    bridge: vmbr0
    firewall: true                # Optional
    disconnect: true              # Optional
    multiqueue: 2                 # Optional
    rate_limit: 250               # Optional (In MB/s)
    vlan_tag: 400                 # Optional

  - id: net1
    bridge: vmbr0
```

Dependencies
------------

We need Ansible version > 2.5 (?) to have the appropriate API of Proxmox modules.

Proxmox VE 5 installed in a the Proxmox node.

Example Playbook
----------------

Let's say, as the vars example, `my_node.my_cluster.org` is our pve node NS (api on port 8006), `my_node` it's pve node name, and `deploy` our Proxmox user in this node, and `pve_virtuals_group` an Ansible group of the machines to define.

Given that:
* virtual machines are named `<machine>.node.my_cluster.org`,
* Name resolutions of PVE machines and node are configured,
* machine's variables are defined (for example in each `host_vars/<machine>/vars/`),
* All new IPs are allocated and routed,

the following playbook creates all the virtual machines declared in the `pve_virtuals_group`,

    - name: create virtual machines declared in  pve_virtuals_group
      hosts: pve_virtuals_group
      remote_user: deploy
      become: yes
      gather_facts: no

      roles:
        - create_kvm


License
-------

(c) Universidad de la República (UdelaR), Red de Unidades Informáticas de la UdelaR en el Interior.

Licenced under GPL-v3

Author Information
------------------

[@ulvida](https://github.com/ulvida)
[@santiagomr](https://github.com/santiagomr)
[@UdelaRInterior](https://github.com/UdelaRInterior)
https://proyectos.interior.edu.uy/
