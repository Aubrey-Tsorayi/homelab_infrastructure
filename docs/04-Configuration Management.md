---
Phase 4: Configuration Management (Ansible)
Goal: Automate software installation on the new VMs.
Date: 2025-12-08
---
## Todo
 - [x] Inventory File: Create hosts.ini on the Pi.
    - [x] Add [servers] group containing 192.168.100.30.
    ```
    [servers]
    192.168.100.30 ansible_user=docker ansible_ssh_private_key_file=~/.ssh/id_rsa
    ```
- [x] SSH Handshake: Ensure Pi can SSH into the new VM (using the key defined in Cloud-Init).
	- `ssh -i ~/.ssh/id_rsa name@<vm-ip>`
- [x] Playbook Creation (site.yml):
    - [x] Task: Update apt cache.
	    - ```yml
	       - name: ensure apt cache is updated
		      apt:
		        update_cache: yes
	      ```
    - [x] Task: Install curl, wget, git.
	    - ```yml
		 - name: Install curl
		   apt:
			 name: curl
		     state: present
	    - name: Install wget
	      apt:
	        name: wget
	        state: present
	
	    - name: Install git
	      apt:
	        name: git
	        state: present
	      ```
    - [x] Task: Install Docker Engine.
    - ```yml
      - name: Install required system packages

      apt:

        name:

          - ca-certificates

          - curl

          - gnupg

        state: present

        update_cache: yes

  

    - name: Create keyrings directory

      file:

        path: /etc/apt/keyrings

        state: directory

        mode: '0755'

  

    - name: Add Docker’s official GPG key

      shell: "curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg"

      args:

          creates: /etc/apt/keyrings/docker.gpg

  

    - name: Set permissions on Docker GPG key

      file:

        path: /etc/apt/keyrings/docker.gpg

        mode: '0644'

  

    - name: Add Docker repo to APT sources

      copy:

        dest: /etc/apt/sources.list.d/docker.list

        content: |

            deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu {{ ansible_distribution_release }} stable

  

    - name: Update APT cache

      apt:

        update_cache: yes

  

    - name: Install Docker Engine + CLI + Containerd

      apt:

        name:

          - docker-ce

          - docker-ce-cli

          - containerd.io

          - docker-buildx-plugin

          - docker-compose-plugin

        state: present

  

    - name: Make sure Docker is running

      service:

        name: docker

        state: started

        enabled: yes
      ```
- [x] Execution: Run ansible-playbook -i hosts.ini site.yml.
	- ```bash
	  monitor@raspberrypi:~/ansible$ ansible-playbook -i hosts.ini site.yml

PLAY [Install curl wget git on Vm] ***************************************************

TASK [Gathering Facts] ***************************************************************
ok: [192.168.100.30]

TASK [ensure apt cache is updated] ***************************************************
changed: [192.168.100.30]

TASK [Install curl] ******************************************************************
ok: [192.168.100.30]

TASK [Install wget] ******************************************************************
ok: [192.168.100.30]

TASK [Install git] *******************************************************************
ok: [192.168.100.30]

TASK [Install required system packages] **********************************************
ok: [192.168.100.30]

TASK [Create keyrings directory] *****************************************************
ok: [192.168.100.30]

TASK [Add Docker’s official GPG key] *************************************************
changed: [192.168.100.30]

TASK [Set permissions on Docker GPG key] *********************************************
ok: [192.168.100.30]

TASK [Add Docker repo to APT sources] ************************************************
changed: [192.168.100.30]

TASK [Update APT cache] **************************************************************
changed: [192.168.100.30]

TASK [Install Docker Engine + CLI + Containerd] **************************************
changed: [192.168.100.30]

TASK [Make sure Docker is running] ***************************************************
ok: [192.168.100.30]

PLAY RECAP ***************************************************************************
192.168.100.30             : ok=13   changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

	  ```

## Lessons
- Ansible is Push Based Architecture; Central server pusher configuration information on target servers.
- What Ansible can do:
	- Configuration Management
	- App Deployment
	- Continuous Delivery
- [Refrences](https://github.com/naveensilver/Ansible)
- 