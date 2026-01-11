---
Phase 7: Maintenance and Upgrades
Goal: Adding alert manager and Node Exporters
Date: 2026-01-11
---
## Todo
- [x] Add peppermint as Help desk to keep tracks of work
	- Followed docker compose and installed using Portainer
- [x] Adding Alert manager to the watchtower
	- `$ docker run --name alertmanager -d -p 9093:9093 quay.io/prometheus/alertmanager`
- [x] Connect Alert manager and Prometheus 
- [x] Set up an Alert to test
- [ ] Install Node exporter in VMs/LXCs (To be visited again)
- [x] Install Proxmox agents to VMs/LXCs
	- Enable Guest Agent in the Proxmox UI. VM -> Options
	- `sudo apt install qemu-guest-agent -y`
	- `sudo systemctl start qemu-guest-agent`
	- `sudo systemctl enable qemu-guest-agent
`

## Lessons
- Prometheus and Alert manager should be on the same docker network, best to use docker compose
- Make sure to update `prometheus.yml` for Prometheus to find the alert manager
- rules/\*yml and alertmanager.yml should ne mounted onto Prometheus and alertmanager respectively.
- Note most of the alerting will be done by grafana and uptime kuma while still figuring out alert manager
- 