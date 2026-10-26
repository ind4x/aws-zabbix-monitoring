# AWS Cloud Monitoring Infrastructure: Centralized Zabbix Deployment

This project implements an enterprise-grade cloud observability and infrastructure
monitoring platform on Amazon Web Services (AWS). It establishes a containerized
Zabbix server stack orchestrating metric ingestion across a hybrid cloud fleet
of Ubuntu Linux and Windows Server EC2 instances.

---

## Technical Architecture

```text
+-------------------------------------------------------------------------------+
|                            Amazon Web Services (AWS)                          |
|                                                                               |
|   Custom Virtual Private Cloud (VPC: 10.0.0.0/16)                             |
|                                                                               |
|   +---------------------------------------+                                   |
|   |         Monitoring Host (EC2)         |                                   |
|   |   Docker Compose Microservices        |                                   |
|   |   - Zabbix Web Frontend (Port 80)     |<================== Web Admin      |
|   |   - Zabbix Server Core (Port 10051)   |                    (Port 80/HTTP) |
|   |   - MySQL Database (Persistent Volume)|                                   |
|   +---------------------------------------+                                   |
|                       ^                   ^                                   |
|                       | (Telemetry Push/  |                                   |
|                       |  Pull: 10050/10051)                                   |
|                       |                   |                                   |
|   +-------------------+---+           +---+-------------------+               |
|   |  Target Linux Node    |           |  Target Windows Node  |               |
|   |  - AWS EC2 (Ubuntu)   |           |  - AWS EC2 (Win 2022) |               |
|   |  - Zabbix Agent (Deb) |           |  - Zabbix Agent (MSI) |               |
|   |  - System Health Dals |           |  - Perfmon Telemetry  |               |
|   +-----------------------+           +-----------------------+               |
+-------------------------------------------------------------------------------+
```

---

## Key Features

- **Single-Pane Observability:** Centralized dashboard tracking CPU utilization, physical memory pressure, disk I/O latency, and interface throughput across hybrid nodes.
- **Containerized Core:** Microservices-based deployment utilizing Docker Compose, ensuring isolated database volumes and reproducible provisioning.
- **Hybrid Platform Agents:** Shell automation for Ubuntu Linux instances and MSI service scripts for Windows Server telemetry collection.
- **VPC Security & Ingress Filtering:** Hardened AWS Security Groups restricting agent-server traffic to internal subnet ports (10050/10051).
- **Proactive Threshold Alerts:** Preconfigured trigger matrices for proactive incident detection before service degradation occurs.

---

## Project Structure

```text
aws-zabbix-monitoring/
├── README.md                          # Technical specification and architecture guide
├── zabbix-server/                     # Docker orchestration manifests
│   ├── docker-compose.yml             # Zabbix Server, Web Frontend, and MySQL services
│   └── .env.example                   # Environment configuration template
├── agents/                            # Automated agent deployment scripts
│   ├── linux-agent/                   # Ubuntu systemd installation and configuration
│   └── windows-agent/                 # Windows Server configuration manifests
├── architecture/                      # Network topology diagrams and security matrices
└── report/                            # Comprehensive architectural audit and verification report
```

---

## Deployment & Setup

### Prerequisites
- AWS Account with EC2, VPC, and Security Group provisioning permissions
- Target EC2 host with Docker Engine and Docker Compose installed

### 1. Launch Central Monitoring Server
1. SSH into the designated monitoring EC2 instance:
   ```bash
   ssh -i key.pem ubuntu@<MONITORING_EC2_IP>
   ```
2. Clone the repository and initialize the stack:
   ```bash
   git clone git@github.com:ind4x/aws-zabbix-monitoring.git
   cd aws-zabbix-monitoring/zabbix-server
   docker compose up -d
   ```

### 2. Provision Target Agents
- **Ubuntu Nodes:** Run `agents/linux-agent/install-agent.sh <MONITORING_SERVER_IP>`
- **Windows Nodes:** Install the Zabbix Agent MSI and specify the Server IP in `zabbix_agentd.conf`.

---

## Access & Governance

The monitoring interface is reachable via the public or internal DNS of the monitoring EC2 instance:
- **URL:** `http://<EC2_PUBLIC_IP>`
- **Initial Credentials:** `Admin` / `zabbix` (enforce password change upon initialization)

---

## Technology Stack

- **Cloud Platform:** Amazon Web Services (EC2, VPC, Security Groups, Elastic IPs)
- **Monitoring Platform:** Zabbix (Server, Agent, Web Interface)
- **Containerization:** Docker, Docker Compose
- **Database:** MySQL Server 8.0
- **Operating Systems:** Ubuntu Linux, Microsoft Windows Server 2022

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Author

- **Author:** Youssef Fellah ([ind4x_](https://github.com/ind4x))
