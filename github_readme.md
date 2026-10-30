<div align="center">

  <h1>🎫 Enterprise Dockerized IT Help Desk Platform</h1>
  <p><strong>A fully containerized, production-grade IT Service Management (ITSM) solution powered by osTicket, MariaDB, and Docker Compose.</strong></p>

  <p>
    <a href="#-project-overview"><img src="https://img.shields.io/badge/Status-Production--Ready-brightgreen?style=for-the-badge&logo=github" alt="Status"></a>
    <a href="#-tech-stack--requirements"><img src="https://img.shields.io/badge/Docker-29.0+-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker"></a>
    <a href="#-tech-stack--requirements"><img src="https://img.shields.io/badge/MariaDB-10.x-003545?style=for-the-badge&logo=mariadb&logoColor=white" alt="MariaDB"></a>
    <a href="#-tech-stack--requirements"><img src="https://img.shields.io/badge/PowerShell-7.0+-5391FE?style=for-the-badge&logo=powershell&logoColor=white" alt="PowerShell"></a>
  </p>

  <hr>
</div>

## 📌 Project Overview

This repository contains a fully containerized IT Help Desk platform built on **osTicket** and **MariaDB** using **Docker Compose**. Designed to simulate enterprise Tier 1/2 IT support operations, the deployment includes department-based ticket routing, SLA performance tracking, role-based access control (RBAC), environment-based secrets isolation, and automated daily database backup scripts with dynamic retention rules.

---

## ✨ Key Features

<table>
  <tr>
    <td width="50%">
      <h3>🐳 Containerized Orchestration</h3>
      <p>Isolated service layers separating application code (osTicket/PHP-FPM/Nginx) and database storage (MariaDB 10.x) across a dedicated Docker bridge network.</p>
    </td>
    <td width="50%">
      <h3>🔒 Hardened Security</h3>
      <p>Externalized secrets using Git-ignored <code>.env</code> files, non-root application execution, protected databases, and restricted administrative network access.</p>
    </td>
  </tr>
  <tr>
    <td width="50%">
      <h3>🏷️ Intelligent Ticket Routing</h3>
      <p>Automated request classification mapped across Help Topics, functional departments, and dynamic priority assignment.</p>
    </td>
    <td width="50%">
      <h3>⏱️ SLA & Escalation Policies</h3>
      <p>Configurable Service Level Agreements (1h Critical, 4h High, 24h Normal, 72h Low) to enforce strict response and resolution targets.</p>
    </td>
  </tr>
  <tr>
    <td width="50%">
      <h3>💾 Automated Backup & Retention</h3>
      <p>Daily automated PowerShell backup routine utilizing <code>mariadb-dump</code> with dynamic 14-day rotation and health checks.</p>
    </td>
    <td width="50%">
      <h3>👥 Role-Based Access Control</h3>
      <p>Tiered administrative and support staff permissions with department-level visibility boundaries.</p>
    </td>
  </tr>
</table>

---

## 🏗️ System Architecture

```text
                       +-----------------------------------+
                       |           Client Browser          |
                       |   http://127.0.0.1:8080 (Client)   |
                       |   http://127.0.0.1:8080/scp (Staff)  |
                       +-----------------+-----------------+
                                         |
                                         | HTTP (Port 8080 -> 80)
                                         v
+-----------------------------------------------------------------------------------+
| Docker Engine (WSL 2 Backend)                                                     |
|                                                                                   |
|  +-------------------------------------+     +---------------------------------+  |
|  |  osTicket Container                 |     |  MariaDB Container              |  |
|  |  (Nginx + PHP-FPM + Cron)           |     |  (Persistent Volume)            |  |
|  |                                     |     |                                 |  |
|  |  Service Name: osticket             |     |  Service Name: mariadb          |  |
|  |                                     | TCP |                                 |  |
|  |  [Internal Application Logic] =====>|====>|  [Database: osticket]           |  |
|  |                                     | 3306|  Port 3306 (Internal Only)     |  |
|  +-------------------------------------+     +---------------------------------+  |
|                                                                                   |
|  Bridge Network: osticket-net                                                     |
+-----------------------------------------------------------------------------------+
                                         |
                                         | Automated Daily Execution
                                         v
                       +-----------------------------------+
                       |     Windows Task Scheduler        |
                       |    backup-osticket.ps1 Script     |
                       |   Target: C:\osticket-backups\    |
                       +-----------------------------------+
```

---

## 🛠️ Tech Stack & Requirements

| Layer | Component / Tool | Version / Spec |
| :--- | :--- | :--- |
| **Operating System** | Windows 10/11 (WSL 2) / Linux / macOS | x86_64 Architecture |
| **Containerization** | Docker Desktop & Docker Compose | Engine v29+ / Compose v2.x+ |
| **Database** | MariaDB Container | 10.x (Persistent Volume) |
| **Application Server** | Nginx & PHP-FPM | Integrated osTicket Image |
| **Automation** | PowerShell / Windows Task Scheduler | Scripting & Daily Cron Task |

---

## 🚀 Getting Started

### 1️⃣ Clone Repository & Prepare Environment

```bash
git clone https://github.com/YOUR_USERNAME/dockerized-osticket-helpdesk.git
cd dockerized-osticket-helpdesk
```

### 2️⃣ Configure Environment Variables (`.env`)

Create a `.env` file in the project root to store credentials safely:

```env
MARIADB_ROOT_PASSWORD=YourUniqueSuperStrongRootPassword123!
MARIADB_PASSWORD=YourUniqueAppPassword456!
ADMIN_USERNAME=helpdeskadmin
ADMIN_PASSWORD=YourUniqueAdminPassword789!
```

> [!IMPORTANT]
> Verify that `.gitignore` contains the following safeguards to protect secrets:
> ```text
> .env
> mariadb-data/
> *.sql
> ```

### 3️⃣ Launch Stack with Docker Compose

```bash
docker compose up -d
```

Verify service status and initialization logs:
```bash
docker compose ps
docker compose logs -f osticket
```

### 4️⃣ Access the Endpoints

<ul>
  <li><strong>Public Support Portal:</strong> <a href="http://127.0.0.1:8080" target="_blank">http://127.0.0.1:8080</a></li>
  <li><strong>Staff / Admin Control Panel:</strong> <a href="http://127.0.0.1:8080/scp" target="_blank">http://127.0.0.1:8080/scp</a></li>
</ul>

---

## ⚙️ Configuration & Operational Workflows

### ⏱️ Service Level Agreements (SLAs)

| SLA Plan Name | Grace Period | Target Operational Scope |
| :--- | :---: | :--- |
| <b style="color: #d9534f;">Critical – 1 Hour</b> | **1 Hour** | System outages, security incidents, network failure |
| <b style="color: #f0ad4e;">High – 4 Hours</b> | **4 Hours** | Workstation hardware failures, blocked user access |
| <b style="color: #0275d8;">Normal – 1 Business Day</b> | **24 Hours** | Software installations, general system requests |
| <b style="color: #5cb85c;">Low – 3 Business Days</b> | **72 Hours** | General inquiries, non-urgent documentation updates |

### 🏢 Department Categorization

- 🔑 **Access Requests:** User onboarding, account provisioning, password resets.
- 💻 **Hardware:** Workstations, laptops, monitors, peripherals, printers.
- 🌐 **Network:** Wi-Fi issues, corporate VPN access, internet connectivity.
- 🛡️ **Security:** Suspicious emails, phishing alerts, malware isolation.
- 📦 **Software & Applications:** Application errors, updates, license allocation.
- 🛠️ **IT Support:** General Tier 1 triage and assistance.

---

## 💾 Backup & Disaster Recovery

An automated disaster recovery process is integrated via `backup-osticket.ps1`. The script executes `mariadb-dump` from inside the MariaDB container without hardcoding credentials or exposing passwords in plaintext.

### 📜 Manual Execution
```powershell
powershell -ExecutionPolicy Bypass -File .\backup-osticket.ps1
```

### ⏰ Automated Daily Task Registration
Execute in Administrator PowerShell to register a daily Task Scheduler job at 9:00 PM with an automated 14-day rotation:

```powershell
$action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument '-ExecutionPolicy Bypass -File "C:\osticket-docker\backup-osticket.ps1"'
$trigger = New-ScheduledTaskTrigger -Daily -At 9PM
$settings = New-ScheduledTaskSettingsSet -StartWhenAvailable
Register-ScheduledTask -TaskName "osTicket Daily Database Backup" -Action $action -Trigger $trigger -Settings $settings -Description "Daily backup of the osTicket MariaDB database"
```

---

## 🖼️ System Screenshots

<div align="center">

  <h3>🎟️ Open Ticket Queue & Management</h3>
  <p><img src="docs/screenshots/open-tickets.png" alt="Open Ticket Queue" width="90%"></p>
  <br>

  <h3>👔 Staff & Agent Directory (RBAC)</h3>
  <p><img src="docs/screenshots/agent-directory.png" alt="Staff Directory" width="90%"></p>
  <br>

  <h3>🏢 Department Configuration</h3>
  <p><img src="docs/screenshots/departments.png" alt="Department Configuration" width="90%"></p>
  <br>

  <h3>🏷️ Help Topics & Automated Ticket Categorization</h3>
  <p><img src="docs/screenshots/help-topics.png" alt="Help Topics" width="90%"></p>
  <br>

  <h3>⏱️ Service Level Agreement (SLA) Matrix</h3>
  <p><img src="docs/screenshots/sla-plans.png" alt="SLA Matrix" width="90%"></p>

</div>

---

## 📄 Operational Runbook & Commands

<details>
<summary><b>🔍 Click to view common CLI operational commands</b></summary>

<br>

```bash
# Stop stack preserving database state
docker compose down

# Restart application container
docker compose restart osticket

# View live container logs
docker compose logs -f --tail=100 osticket

# Manual database dump check
docker exec osticket-db sh -c 'mariadb-dump -h 127.0.0.1 -u osticket -p"$MARIADB_PASSWORD" osticket' > manual-backup.sql
```

</details>

---

## 👤 Author

<div align="center">
  <h3>Bibin</h3>
  <p><strong>Technical Support & Infrastructure Specialist</strong></p>
  <p>
    <a href="https://github me" target="_blank"><img src="https://img.shields.io/badge/GitHub-Profile-181717?style=for-the-badge&logo=github" alt="GitHub Profile"></a>
  </p>
</div>