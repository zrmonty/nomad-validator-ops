# Nomad Validator Ops

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Ansible](https://img.shields.io/badge/ansible-2.14+-red.svg)
![Status](https://img.shields.io/badge/status-active_development-green.svg)

**Nomad Validator Ops** is the core infrastructure repository for **Nomad Cybersecurity Group**. It contains the Ansible playbooks and configuration management code required to provision, harden, and maintain high-performance Ethereum validator nodes on bare-metal infrastructure (specifically Hetzner and Latitude.sh).

This pipeline replaces manual "Click-Ops" with immutable Infrastructure as Code (IaC), ensuring consistent configuration drift management and rapid disaster recovery.

## 🚀 Architecture

The stack is designed for **maximum uptime** and **slashing protection**, prioritizing bare-metal performance over cloud virtualization overhead.

* **Infrastructure:** Bare Metal (Hetzner AX/PX Line)
* **OS:** Ubuntu 22.04 LTS (Minimal)
* **Orchestration:** Docker Compose (via Ansible)
* **Execution Client:** [Reth](https://github.com/paradigmxyz/reth) (Rust Ethereum) - Chosen for high I/O performance.
* **Consensus Client:** [Lighthouse](https://github.com/sigp/lighthouse) (Rust)
* **Monitoring:** Prometheus, Node Exporter, Grafana, and custom alerts.

## ✨ Features

* ** automated Provisioning:** Bootstraps a fresh server to a fully synced node state.
* **Security Hardening:**
    * Disables Root SSH & Password Authentication.
    * Configures UFW (Uncomplicated Firewall) to allow only P2P (30303/9000) and VPN traffic.
    * Applies kernel-level networking tuning (`sysctl`) for high-throughput P2P connections.
* **Client Management:** Automated Docker container lifecycle management for Reth and Lighthouse.
* **Observability:** Pre-configured dashboards for system metrics (IOPS, CPU, RAM) and blockchain metrics (Peer count, Sync distance).

## 🛠️ Prerequisites

* **Ansible:** v2.14 or newer.
* **SSH Access:** A distinct SSH key pair for the target server.
* **Python:** v3.8+ on the control node.

## 📦 Installation & Usage

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/zrmonty/nomad-validator-ops.git](https://github.com/zrmonty/nomad-validator-ops.git)
    cd nomad-validator-ops
    ```

2.  **Configure Inventory:**
    Copy the example inventory and add your server IP.
    ```bash
    cp inventory/hosts.example.ini inventory/hosts.ini
    # Edit hosts.ini with your Hetzner IP address
    ```

3.  **Run the Bootstrap Playbook:**
    This runs the initial security hardening and user creation.
    ```bash
    ansible-playbook playbooks/01_bootstrap.yml -i inventory/hosts.ini
    ```

4.  **Deploy Validator Stack:**
    Deploys the Docker containers for Reth and Lighthouse.
    ```bash
    ansible-playbook playbooks/02_deploy_node.yml -i inventory/hosts.ini
    ```

## 📂 Project Structure

```text
.
├── inventory/              # Host definitions (Production/Staging)
├── playbooks/              # Ansible Playbooks
│   ├── 01_bootstrap.yml    # System Hardening & Dependencies
│   └── 02_deploy_node.yml  # Client Deployment
├── roles/                  # Ansible Roles
│   ├── security/           # UFW, Fail2Ban, SSH Config
│   ├── docker/             # Docker Engine Installation
│   └── ethereum/           # Reth/Lighthouse Configs
└── vars/                   # Global Variables (Client versions, Ports)
```
