---
Phase 5: Kubernetes
Goal: Deploy a RAM-efficient cluster
Date: 2025-12-09
---
## Todo
- [x] Architecture Decision: K3s (Lightweight Kubernetes).
- [x] Master Node Setup: Install K3s on the Docker Host VM (192.168.1.30).
    - [x] curl -sfL https://get.k3s.io | sh -
	    - verify installation: `sudo k3s kubectl get node`
- [x] Prepare Proxmox Host
	- ```conf
	  # vi /etc/sysctl.d/99-k3s.conf
		net.bridge.bridge-nf-call-iptables = 1
		net.bridge.bridge-nf-call-ip6tables = 1
		vm.swapiness=0
		net.ipv4.ip_forward = 1
	  ```
	  - Disable swap: `swapoff -a`
	  - ```
	    # nano /etc/fstab
	    /dev/pve/root / ext4 errors=remount-ro 0 1
		UUID=F9C1-B5A1 /boot/efi vfat defaults 0 1
		#/dev/pve/swap none swap sw 0 0
		proc /proc proc defaults 0 0
	    ```
	- Reboot
- [x] Worker Node Setup (LXC):
    - [x] Create an LXC container on Proxmox (Ubuntu Template, 2GB RAM).
	    - Downloaded template of the Proxmox GUI (simplicity)
	    - Created `lxc.tf` to design the infrastructure
    - [x] Important: Enable Nesting, FUSE, and keyctl in LXC Options.
	    - Uses provider aliases since Nesting, FUSE and keyctl required root user.
	    - Make the container unprivileged
	    - After creation append `/etc/pve/lxc/$ID.conf` on proxmox
		    - ```conf
		      _lxc.apparmor.profile: unconfined  
				lxc.cgroup2.devices.allow: a  
				lxc.cap.drop:  
				lxc.mount.auto: "proc:rw sys:rw"_
		      ```
		- create `/etc/rc.local` on the container
			- ```local
			  #!/bin/sh -e
				# Kubeadm 1.15 needs /dev/kmsg to be there, but it’s not in lxc, but we can just use /dev/console instead
				# see: https://github.com/kubernetes-sigs/kind/issues/662if [ ! -e /dev/kmsg ]; then
				ln -s /dev/console /dev/kmsg
				fi# https://medium.com/@kvaps/run-kubernetes-in-lxc-container-f04aa94b6c9c
				mount --make-rshared /
			  ```
		- `chmod +x /etc/rc.local`
    - [x] Join LXC to Master using K3S_URL and K3S_TOKEN.
	    - ` curl -sfL https://get.k3s.io | K3S_URL=https://192.168.100.30:6443 K3S_TOKEN=TOKEN sh -`
	    - check for token on master: `sudo cat /var/lib/rancher/k3s/server/node-token`
- [x] Verification: Run kubectl get nodes on the Master VM.

## Lessons 
- How to use aliases when you using multiple users and providers
- `nesting`: Allows a container to run containers itself.
- `fuse`: Enables the **FUSE filesystem interface** inside the container.
	- FUSE (Filesystem in Userspace) is a software interface for Unix and Unix-like operating systems that allows non-privileged users to create their own filesystems without modifying kernel code
- `keyctl`: Enables the **key management syscall** inside the container.
- `proxmox_lxc` (ended up using)
	- older Telmate Proxmox Provider
	- Best use for basic LXC provisioning and existing Terraform configs using older provider versions
- `proxmox_lxc_guest`
	- Newer but experimental
	- best use for K3s / container workloads, homelab labs, cloud-init style
- Kubernetes will not initialize if the swap is not disabled.
- [Preparing the container and host for k3s](https://kevingoos.medium.com/kubernetes-inside-proxmox-lxc-cce5c9927942)
- [Running k3s in the container](https://kevingoos.medium.com/installing-k3s-in-an-lxc-container-2fc24b655b93)
- 