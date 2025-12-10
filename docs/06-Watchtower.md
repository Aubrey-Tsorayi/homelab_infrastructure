---
Phase 6: The Watchtower (Monitoring)
Goal: Use the Pi to visualize the Dell's health
Date: 2025-12-09
---
## Todo
- [x] Create a new role to monitor
	- `pveum user add prometheus@pve`
	- `pveum aclmod / -user prometheus@pve -role PVEAuditor`
	- `pveum user token add prometheus@pve prometheus-token --privsep=0`
- [x] Proxmox Metrics: Install prometheus-pve-exporter on the Pi (Python venv or Docker).
    - [x] Configure it to point to 192.168.1.20 (Dell).
- [x] Docker Compose (on Pi):
	- [x] Prometheus: Mount prometheus.yml config.
    - [x] Grafana: Port 3000.
    - [x] Uptime Kuma: Port 3001.
- [x] Data Linking:
	- [x] Add Prometheus as a Data Source in Grafana.
    - [x] Import "Proxmox" Dashboard ID (e.g., 10347) from Grafana community.
- [ ] Alerting: Set up a Grafana alert if Dell RAM usage > 90%.

## Lessons
- When creating creating the token ensure `--privsep=0` , this ensures that the token has full permissions like the user
- `--privsep` controls **how privileges are applied to the token** compared to the parent user
	- `--privsep=0`: Token inherits **all the roles** assigned to the user at their ACL paths. Full permissions like the user.
	- `--privsep=1`: Token privileges are **reduced**, and only **what’s explicitly allowed** for API tokens is granted.
- 