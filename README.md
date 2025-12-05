# ☁️ Hybrid Cloud & DevOps Infrastructure Lab

![Status](https://img.shields.io/badge/Status-Active-success)
![Terraform](https://img.shields.io/badge/IaC-Terraform-purple)
![Ansible](https://img.shields.io/badge/Config-Ansible-red)
![Kubernetes](https://img.shields.io/badge/Orchestration-K3s-blue)
![Azure](https://img.shields.io/badge/Cloud-Azure_Arc-0078D4)

## 📖 Project Overview
This repository documents my journey building a professional-grade, hybrid-cloud infrastructure lab. The goal of this project is to simulate a real-world enterprise environment to apply concepts required for **LPIC-3**, **KCNA**, and **Azure Administrator** certifications.

Unlike typical homelabs, this infrastructure treats **Hardware as Code**. The entire environment is provisioned via Terraform and configured via Ansible, mimicking modern DevOps workflows.

---

## 🏗️ Architecture: The "Control Plane" Model

To maximize resource efficiency and simulate a distributed network, I have split the infrastructure into two distinct physical zones:

1.  **The Compute Plane (Dell OptiPlex):** dedicated strictly to running workloads (VMs, Containers, Kubernetes).
2.  **The Control Plane (Raspberry Pi 5):** dedicated to management, automation, and observability.

### 🛠️ Hardware Inventory

| Device | Role | Specs | OS / Hypervisor |
| :--- | :--- | :--- | :--- |
| **Dell OptiPlex 7050** | Compute Node | i5-7500T, 16GB DDR4, 256GB NVMe | **Proxmox VE 8.x** |
| **Raspberry Pi 5** | Control Node | 8GB LPDDR4X | **Ubuntu Server 24.04 LTS** |

---

## 🔌 Network Topology & IP Management

| Hostname | IP Address | Description | Management |
| :--- | :--- | :--- | :--- |
| `pve-host` | `192.168.1.20` | Proxmox Hypervisor | Local GUI |
| `pi-control` | `192.168.1.10` | Terraform/Ansible Controller | SSH |
| `vm-docker-01` | `192.168.1.30` | K3s Master / Docker Host | Terraform |
| `lxc-k3s-w1` | `192.168.1.31` | K3s Worker Node | Proxmox API |

---

## 🧰 The Tech Stack

### Infrastructure as Code (IaC)
-   **Terraform:** Manages the lifecycle of Virtual Machines and LXC containers on Proxmox using the `telmate/proxmox` provider.
-   **Cloud-Init:** bootstrapping OS images with SSH keys and network configs.

### Configuration Management
-   **Ansible:** Automates the post-provisioning setup (installing Docker, K3s agents, and system patches).

### Container Orchestration
-   **K3s:** A lightweight Kubernetes distribution optimized for edge/IoT, running a mixed cluster of VMs and LXC containers.
-   **Docker Compose:** Used for deploying the monitoring stack.

### Observability & Monitoring
-   **Prometheus:** Scrapes metrics from Proxmox Node Exporters.
-   **Grafana:** Visualizes cluster health, temperatures, and resource usage.
-   **Uptime Kuma:** External "ping" monitoring for service uptime.

### Hybrid Cloud
-   **Azure Arc:** Extends Azure management capabilities to the local on-premise servers, allowing for centralized inventory and policy management.

---

## 📂 Repository Structure

```text
.
├── ansible/               # Playbooks for software configuration
│   ├── inventory/         # Hosts file
│   └── site.yml           # Main playbook
├── terraform/             # IaC definitions
│   ├── main.tf            # Provider and resource inputs
│   └── variables.tf       # Secrets and customizations
├── docs/                  # Detailed implementation notes
│   ├── troubleshooting.md # Log of errors and fixes
│   └── architecture.md    # Network diagrams
└── README.md              # You are here