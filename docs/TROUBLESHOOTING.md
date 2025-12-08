## Setting up Terraform user and roles
root@proxmox:~# pveum acl mod / -user terraform-prov@pve -role TerraformProv-privs
ACL update failed: role 'TerraformProv-privs' does not exist
### Fix
- `pveum role list`
- Role was not succefully created becuase used the wrong comand
	- updated command: `pveum role add TerraformProv-privs --privs "VM.Allocate VM.Clone VM.Config.CDROM VM.Config.CPU VM.Config.Cloudinit VM.Config.Disk VM.Config.HWType VM.Config.Memory VM.Config.Network VM.Config.Options VM.Audit VM.PowerMgmt" (corrected in the final docs)
		- Note: 
			- the `--privs` is needed before adding the privileges
			- the privileges are entered as individuals not one long string
- 
`

## Terraform plan
- ```bash
  monitor@raspberrypi:~/homelab-infra$ terraform plan
╷
│ Error: Invalid function argument
│
│   on main.tf line 45, in resource "proxmox_vm_qemu" "docker_host":
│   45:     sshkeys = file("~/.ssh/id_rsa.pub")
│     ├────────────────
│     │ while calling file(path)
│
│ Invalid value for "path" parameter: no file exists at "~/.ssh/id_rsa.pub"; this
│ function works only with files that are distributed as part of the configuration
│ source code, so if this file will be created by a resource in this configuration
│ you must instead obtain this result from an attribute of that resource.

  ```
  - Terraform expects **a real, absolute path**, and it must point to a file that **already exists** at plan/apply time.
	  - `sshkeys = file("/home/monitor/.ssh/id_rsa.pub")`
- Generate a new ssh key pair
	- `ssh-keygen -t rsa -b 4096 -f /home/monitor/.ssh/id_rsa`
- Set up permissions
	- ```bash
	  chmod 700 /home/monitor/.ssh
	  chmod 600 /home/monitor/.ssh/id_rsa
	  chmod 644 /home/monitor/.ssh/id_rsa.pub
	  ```

- ```bash
  monitor@raspberrypi:~/homelab-infra$ terraform plan
╷
│ Error: "name", must only contain alphanumerics, hyphens and dots [docker_host]
│
│   with proxmox_vm_qemu.docker_host,
│   on main.tf line 18, in resource "proxmox_vm_qemu" "docker_host":
│   18:   name = "docker_host"
│
╵
╷
│ Error: type must be one of 'disk', 'cdrom', 'cloudinit'
│
│   with proxmox_vm_qemu.docker_host,
│   on main.tf line 28, in resource "proxmox_vm_qemu" "docker_host":
│   28:     type = "scsi"
│
╵
╷
│ Error: slot must be one of 'ide0', 'ide1', 'ide2', 'sata0', 'sata1', 'sata2', 'sata3', 'sata4', 'sata5', 'scsi0', 'scsi1', 'scsi2', 'scsi3', 'scsi4', 'scsi5', 'scsi6', 'scsi7', 'scsi8', 'scsi9', 'scsi10', 'scsi11', 'scsi12', 'scsi13', 'scsi14', 'scsi15', 'scsi16', 'scsi17', 'scsi18', 'scsi19', 'scsi20', 'scsi21', 'scsi22', 'scsi23', 'scsi24', 'scsi25', 'scsi26', 'scsi27', 'scsi28', 'scsi29', 'scsi30', 'virtio0', 'virtio1', 'virtio2', 'virtio3', 'virtio4', 'virtio5', 'virtio6', 'virtio7', 'virtio8', 'virtio9', 'virtio10', 'virtio11', 'virtio12', 'virtio13', 'virtio14', 'virtio15'
│
│   with proxmox_vm_qemu.docker_host,
│   on main.tf line 30, in resource "proxmox_vm_qemu" "docker_host":
│   30:     slot = 0
│
  ```
  - DO NOT USE UNDERSCORES FOR VM NAMES
	  - `name="docker-host"`
- `type=disk`
- `slot = "scsi0"`