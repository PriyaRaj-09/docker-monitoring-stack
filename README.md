
# Docker Monitoring Stack

Server and container monitoring on AWS EC2 using Prometheus, Grafana, Node Exporter and cAdvisor, all running in Docker Compose.

## Architecture
Node Exporter (server metrics) + cAdvisor (container metrics) → Prometheus (scrapes every 15s) → Grafana (dashboards)

| Service | Port | Access |
| --- | --- | --- |
| Grafana | 3000 | Public, password protected |
| Prometheus | 9090 | Restricted to my IP |
| Node Exporter | 9100 | Internal only |
| cAdvisor | 8080 | Internal only |

## Dashboards
- Node Exporter Full (Grafana ID 1860): CPU, memory, disk, network
- Cadvisor exporter (Grafana ID 14282): CPU and memory per container

## How to run
```bash
git clone https://github.com/PriyaRaj-09/docker-monitoring-stack.git
cd docker-monitoring-stack
docker compose up -d
```

## Lessons learned
- Monitoring spotted the root disk at 94%; grew the EBS volume from 8 to 16 GB live with `growpart` and `resize2fs`
- Added 1 GB swap so the 1 GB instance doesn't run out of memory
- cAdvisor v0.49.1 couldn't read containers on Docker 29's containerd image store; fixed by setting `"containerd-snapshotter": false` in `/etc/docker/daemon.json`
- AWS "My IP" picked an IPv6 address; the browser used IPv4, so the rule needed an IPv4 /32
- Load test with a CPU stress container: the spike showed up per container in Grafana
