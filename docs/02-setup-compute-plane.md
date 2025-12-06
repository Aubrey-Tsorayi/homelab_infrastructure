---
Date: 2025-12-06
Phase 2: The Compute Target
Goal: Prepare the Dell to receive automated commands from the Pi
---
## Specs

| Name      | Dell OptiPlex 7050 d10U |
| --------- | ----------------------- |
| RAM       | 16 GB DDR4              |
| Storage   | 256 GB SSD              |
| Chip      | Intel 7th Gen Core i5   |
| CPU Cores | 2                       |

## Todo
- [x] Static IP: Ensure Dell Proxmox has a Static IP (Recommended: 192.168.1.20). 
	- Static IP was set during the Proxmox VE installation
- [x] Cloud-Init Image: Reference help [# Perfect Proxmox Template with Cloud Image and Cloud Init](https://technotim.live/posts/cloud-init-cloud-image/)
	- [x] Download a Cloud-Init enabled Ubuntu 22.04 or Debian 12 image. 
		-  Ubuntu image: `wget https://cloud-images.ubuntu.com/releases/22.04/release/ubuntu-22.04-server-cloudimg-amd64.img`
	- [x] Create new virtual machine
		- `qm create 9000 --memory 2048 --core 1 --name ubuntu-cloud --net0 virtio,bridge=vmbr0`
	- [x] Import disk to local-lvm storage. 
		-  `qm disk import 8000 noble-server-cloudimg-amd64.img local`
	- [x] Attach disk to the vm as a scsi drive
		- `qm set 9000 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9000-disk-0`
	- [x] Add cloud init drive
		- `qm set 9000 --ide2 local-lvm:cloudinit`
	- [x] Make cloud init drive bootable and restrict BIOS to boot from disk only
		- `qm set 9000 --boot c --bootdisk scsi0`
	- [x] Add serial console
		- `qm set 9000 --serial0 socket --vga serial0`
- [x] Convert to template (ID: 9000). Crucial for Terraform cloning. 
	- `qm template 9000`
- [x] Terraform User & Role: 
	- [x] Create role: `pveum role add TerraformProv-privs --privs "VM.Allocate VM.Clone VM.Config.CDROM VM.Config.CPU VM.Config.Cloudinit VM.Config.Disk VM.Config.HWType VM.Config.Memory VM.Config.Network VM.Config.Options VM.Audit VM.PowerMgmt"`
	- [x] Create user: `pveum user add terraform-prov@pve `
	- [x] Generate API Token: `pveum user token add terraform-prov@pve terraform-token --privsep 0` 
	- [x] SAVE THE SECRET KEY IMMEDIATELY. 
- [x] Permission Link: `pveum acl mod / -user terraform-prov@pve -role TerraformProv-privs`

## Things learnt
- **Cloud-init** is the industry-standard, open-source tool for automating the initial setup and configuration of Linux virtual machines (VMs) and servers as they boot for the first time, allowing you to set hostnames, install packages, create users, configure networks, and run scripts automatically, making deployments consistent and efficient across various cloud platforms and environments.
	- Automates first boot task
	- Customizes instances, i.e. configure username, networking, users, ssh keys
	- Install software
	- Run scripts
- The proxmox privileges granted are:
	- **`VM.Allocate`**: Allows creating new VMs.
	- **`VM.Clone`**: Allows cloning existing VMs/templates.
	- **`VM.Config.*` (CDROM, CPU, Cloudinit, Disk, HWType, Memory, Network, Options)**: Provides fine-grained control over various VM configuration settings.
	- **`VM.Audit`**: Allows viewing VM configuration and status details, often a prerequisite for console access and other operations.
	- **`VM.PowerMgmt`**: Allows managing the power state of the VM (start, stop, reset, shutdown).