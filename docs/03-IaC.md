---
Date: 2025-12-07
Phase 3: " Infrastructure as Code (Terraform)"
Goal: " Spin up the lab infrastructure without touching the Proxmox GUI."
---
## Todo
- [x] Project Setup: Create folder ~/homelab-infra on the Pi.
	- Create files :
	- `main.tf` : host of providers
	- `variables.tf` : 
	- `credentials.auto.tfvars`
	- `cloud-init.tf` : defining the VM
- [x] Provider Config (main.tf):
    - [x] Configure telmate/proxmox provider.
    - [x] Set pm_api_url = "https://192.168.1.20:8006/api2/json".
- [x] Environment Variables: Export PM_PASS or PM_API_TOKEN_SECRET in the shell (Do not hardcode secrets!).
- [x] Resource Definition: Define resource "proxmox_vm_qemu" "docker_host".
    - [x] RAM: 4096 (4GB).
    - [x] Cores: 2.
    - [x] IP: 192.168.1.30 (via Cloud-Init).
    - 
- [x] Execution:
    - [x] terraform init
    - [x] terraform plan -out=plan.tfplan (Verify output looks correct).
    - [x] terraform apply (Watch the magic happen).
- [x] Verification: Ping 192.168.1.30 from the Pi.

## Lessons
- Always use the latest version of a provider or the one below just avoid errors that may conflict with a new release of proxmox
- Privilege management is crucial to a successful plan and deployment 
- When encounter problems reference [Proxmox Forums](https://forum.proxmox.com/)
- [Teraform Template Structure](https://github.com/tech-the-lazy-automator/homelab-automation-series/blob/main/34-automate-cloudinit-vm-with-terraform/envs/proxmox/cloud-init.tf)
- 