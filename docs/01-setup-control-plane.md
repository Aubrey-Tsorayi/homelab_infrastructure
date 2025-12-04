---
Date: 2025-12-05
Phase 1: The Control Plane (Raspberry Pi Setup)
Goal: Prepare the Pi to manage the network and host the “brains” of the operation.
---
## Specs

| Name    | Raspberry Pi 5          |
| ------- | ----------------------- |
| RAM     | 8 GB                    |
| Storage | 32 GB (sd card)         |
| OS      | Ubuntu Server 24.04 LTS |
| IP      | 192.168.100.3           |

## Todo

- [x] OS Installation: Install Ubuntu Server 24.04 LTS (64-bit) on the Pi 5. 
	- Used the Raspberry PI Imager
	- Ubuntu OS was found on the general purpose section of the imager.
	- Set SSH that uses username and password.
- [x] Network Config: Set a Static IP for the Pi. 
	- `nano /etc/netplan/50-cloud-init.yaml`
	- ![[Pasted image 20251205000244.png]]
	- `sudo netplan apply`
- [ ] SSH Access: configure SSH key-based authentication (generate keys on your laptop, copy to Pi). 
- [x] Update System: Run sudo apt update && sudo apt upgrade-y. 
- [x] Install Git, tmux: `sudo apt install git tmux`. 
- [x] Install Terraform: 
	- [x] Installed required packages: `sudo apt update && sudo apt install -y gnupg software-properties-common`
	- [x] Add HashiCorp GPG key. 
		- `curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
	- [x] Add repository. 
		- ```bash
		  echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
			https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
			sudo tee /etc/apt/sources.list.d/hashicorp.list
	```
	- [x] `sudo apt update && sudo apt install terraform`. 
- [x] Install Ansible: `sudo apt install ansible`. 
- [x] Install Docker & Compose: (For the monitoring stack later). 
	- Since it is a preinstall Ubuntu OS, remove old packages before installing new ones : `sudo apt remove -y docker docker-engine docker.io containerd runc`
	- [x] Add Docker’s official GPG key
	- ```bash
	  sudo install -m 0755 -d /etc/apt/keyrings
	  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
	  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
	  sudo chmod a+r /etc/apt/keyrings/docker.gpg
	  ```
	- [x] Add Docker repo. 
		- ```bash
		  echo \
			  "deb [arch=$(dpkg --print-architecture) \
			  signed-by=/etc/apt/keyrings/docker.gpg] \
			  https://download.docker.com/linux/ubuntu \
			  $(lsb_release -cs) stable" | \
			  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
		  ```
	- [x] `sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`. 
	- [x] Add user to docker group: `sudo usermod-aG docker $USER`

