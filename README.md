# IT Infrastructure Project — Load Balanced Web Server

## Project Overview
This project involves designing and implementing a complete IT infrastructure
that includes a load balanced web server environment. The infrastructure
consists of two web servers and a load balancer, connected through a managed
network with separate VLANs for wired and wireless clients.

---

## Project Purpose
The purpose of this project is to:
- Build a real world server infrastructure from scratch
- Implement load balancing to distribute traffic across multiple web servers
- Ensure high availability so if one server fails the other continues working
- Apply network security through firewall rules and VLANs
- Test the infrastructure under stress to measure real performance
- Simulate a penetration test to evaluate server resilience

---

## Project Team
| Role | Task |
|------|------|
| Web Server Team (2 students) | Configure web1 and web2, install Apache, deploy website |
| Load Balancer (1 student) | Configure Nginx reverse proxy to distribute traffic |
| Penetration Testing | Perform DDoS attack to test server resilience |

---

## Project Structure
IT Infrastructure Project
│
├── Network Layer
│   ├── Server VLAN
│   │   ├── Web Server 1 (172.24.30.11) — Apache
│   │   ├── Web Server 2 (172.24.30.12) — Apache
│   │   └── Load Balancer (172.24.30.100) — Nginx
│   ├── Wired Client VLAN (VLAN 10)
│   └── Wireless Client VLAN (VLAN 20)
│
├── Server Layer
│   ├── web1 — Ubuntu Server, Apache, MariaDB, Web App
│   ├── web2 — Ubuntu Server, Apache, MariaDB, Web App
│   └── loadbalancer — Ubuntu Server, Nginx Reverse Proxy
│
└── Testing Layer
├── Functional Testing — curl, browser
├── Performance Testing — ApacheBench (ab)
└── Penetration Testing — DDoS simulation

---

## Traffic Flow
Client (VLAN 10 or VLAN 20)
↓
Load Balancer (172.24.30.100)
Nginx Reverse Proxy
↓              ↓
Web Server 1      Web Server 2
(172.24.30.11)    (172.24.30.12)
Apache            Apache
MariaDB           MariaDB

---

## IP Address Table
| Device | IP Address | OS | Software |
|--------|-----------|-----|---------|
| Load Balancer | 172.24.30.100 | Ubuntu Server | Nginx |
| Web Server 1 | 172.24.30.11 | Ubuntu Server | Apache, MariaDB |
| Web Server 2 | 172.24.30.12 | Ubuntu Server | Apache, MariaDB |

---

## Load Balancer Server Documentation
**Configured by:** Jana
**IP Address:** 172.24.30.100
**OS:** Ubuntu Server
**Software:** Nginx 1.24.0

---

## Network Configuration
| Setting | Value |
|---------|-------|
| IP Address | 172.24.30.100/24 |
| Gateway | 172.24.30.1 |
| DNS | 8.8.8.8, 8.8.4.4 |
| Ethernet Interface | eno1 |
| WiFi Interface | wlo1 |

---

## Architecture
Client Request
↓
Load Balancer (172.24.30.100) — Nginx Reverse Proxy
↓                    ↓
Web Server 1          Web Server 2
(172.24.30.11)        (172.24.30.12)
Apache                Apache

---

## Load Balancing Method
- **Type:** Reverse Proxy
- **Algorithm:** Round Robin (Default Nginx)
- **How it works:**
  - Request 1 → Web Server 1
  - Request 2 → Web Server 2
  - Request 3 → Web Server 1
  - Request 4 → Web Server 2
  - Alternates equally between both servers

---

## Software Installation
### Nginx
- Installed as reverse proxy load balancer
- Enabled at boot so it starts automatically after reboot
- Default site removed and replaced with custom load balancer config

### Monitoring Tools Installed
| Tool | Purpose |
|------|---------|
| htop | Real time CPU and memory monitoring |
| net-tools | Network utilities |
| sysstat | System statistics and CPU usage |
| apache2-utils | Stress testing with ApacheBench (ab) |

---

## Firewall Configuration (UFW)
| Port | Protocol | Purpose | Status |
|------|----------|---------|--------|
| 22 | TCP | SSH remote access | ALLOW |
| 80 | TCP | HTTP web traffic | ALLOW |
| 443 | TCP | HTTPS secure traffic | ALLOW |

---

## Nginx Load Balancer Configuration
```nginx
upstream webservers {
    server 172.24.30.11;
    server 172.24.30.12;
}

server {
    listen 80;
    server_name 172.24.30.100;

    location / {
        proxy_pass http://webservers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
### Configuration Explained
- `upstream webservers` — defines the group of backend web servers
- `server 172.24.30.11` — web server 1 in the group
- `server 172.24.30.12` — web server 2 in the group
- `listen 80` — accepts incoming traffic on HTTP port 80
- `proxy_pass` — forwards requests to the web server group
- `proxy_set_header Host` — passes the original host header to backend
- `proxy_set_header X-Real-IP` — passes the real client IP to backend

---

## Security Measures
1. **Firewall** — only ports 22, 80 and 443 allowed, all others blocked
2. **Non-root admin user** — created dedicated admin user instead of root
3. **Reverse proxy** — backend web servers hidden from clients
4. **Netplan permissions** — config file secured with chmod 600

---

## Steps Performed In Order
1. Configured network interfaces (eno1 static IP, wlo1 WiFi)
2. Set static IP 172.24.30.100 with gateway 172.24.30.1
3. Updated all system packages
4. Created non-root admin user with sudo privileges
5. Installed and enabled Nginx web server
6. Configured UFW firewall rules
7. Created load balancer configuration file
8. Enabled load balancer and removed default Nginx site
9. Installed monitoring tools (htop, sysstat, apache2-utils)
10. Verified all services running correctly
11. Connected to web team servers and tested load balancing
12. Ran stress test and recorded performance results

---

## Verification Commands Used
```bash
ip addr show eno1          # verify static IP
sudo systemctl status nginx # verify nginx running
sudo nginx -t              # verify config valid
sudo ufw status            # verify firewall rules
sudo ss -tlnp | grep 80   # verify port 80 listening
sudo tail -f /var/log/nginx/access.log  # verify live traffic
```

---

## Performance Testing Results
### Stress Test Details
- **Tool used:** ApacheBench (ab)
- **Command:** `ab -n 1000 -c 50 http://172.24.30.100/`
- **Total requests:** 1000
- **Concurrent users:** 50

### Results
| Metric | Result |
|--------|--------|
| Total requests | 1000 |
| Failed requests | 0 |
| Requests per second | 7482 |
| Average response time | 6.688 ms |
| Fastest response | 4 ms |
| Slowest response | 11 ms |
| Total test duration | 0.134 seconds |
| Data transferred | 1125000 bytes |

### System Resources During Test
| Resource | Usage |
|----------|-------|
| CPU usage | ~0% |
| RAM total | 15624 MB |
| RAM used | 1010 MB |
| RAM free | 14491 MB |

---

## Live Traffic Verification
After connecting with web team, live traffic log showed:
- Requests successfully forwarded to 172.24.30.11
- Requests successfully forwarded to 172.24.30.12
- All requests returned HTTP 200 (success)
- Round robin distribution confirmed working
<img width="1116" height="647" alt="WhatsApp Image 2026-05-28 at 10 11 40 PM" src="https://github.com/user-attachments/assets/c788c0a5-0c70-4a76-8f8f-a98bae87ca17" />

---

## Conclusion
The Nginx load balancer was successfully configured as a reverse proxy
distributing traffic between two Apache web servers. The system handled
1000 concurrent requests with 0 failures and achieved 7482 requests per
second with only 6.688ms average response time. CPU usage remained at
nearly 0% throughout the stress test demonstrating excellent performance
and stability of the load balancer configuration.
