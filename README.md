📌 Project OverviewThis repository contains a fully containerized, production-ready IT Help Desk platform built on osTicket and MariaDB using Docker Compose. Designed to simulate real-world Tier 1/2 IT support operations, the deployment includes department-based ticket routing, SLA performance tracking, role-based access control (RBAC), environment-based secrets isolation, and automated daily database backup scripts with dynamic retention rules.✨ Key Features🐳 Containerized Orchestration: Isolated service layers separating application code (osTicket/PHP-FPM/Nginx) and database storage (MariaDB 10.x).🔒 Security Hardened: Externalized secrets using Git-ignored .env files, protected root and application databases, and restricted administrative network access.🏷️ Intelligent Ticket Routing: Automated routing mapped across Help Topics, custom departments, and priority levels.⏱️ SLA & Escalation Policies: Configurable Service Level Agreements (1h Critical, 4h High, 24h Normal, 72h Low) to enforce response and resolution timelines.💾 Automated Backup & Retention: Automated daily PowerShell backup routine using mariadb-dump with automatic 14-day rotation and verification checks.👥 Role-Based Access Control (RBAC): Tiered administrative and support staff permissions with limited department visibility.🏗️ Architecture                       +-----------------------------------+
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
🛠️ Tech Stack & RequirementsOS: Windows 10/11 (WSL 2 Enabled) or Linux/macOSContainerization: Docker Desktop v29+ / Docker Compose v2+ / v5+Database: MariaDB (Latest Stable)Web Server: Nginx 1.18+ / PHP 7.3+ (Integrated within OS Container)Automation: PowerShell 7+ / Windows Task Scheduler🚀 Getting Started1. Clone Repository & Setup Structuregit clone https://github.com/YOUR_USERNAME/dockerized-osticket-helpdesk.git
cd dockerized-osticket-helpdesk
2. Configure Environment Variables (.env)Create a local .env file in the project root directory (Do not commit this file to Git):MARIADB_ROOT_PASSWORD=YourUniqueSuperStrongRootPassword123!
MARIADB_PASSWORD=YourUniqueAppPassword456!
ADMIN_USERNAME=helpdeskadmin
ADMIN_PASSWORD=YourUniqueAdminPassword789!
Ensure .gitignore contains the following safeguards:.env
mariadb-data/
*.sql
3. Launch Docker Compose Stackdocker compose up -d
Verify service status and container initialization:docker compose ps
docker compose logs -f osticket
4. Access the Help DeskPublic Support Portal: http://127.0.0.1:8080Staff / Admin Control Panel: http://127.0.0.1:8080/scp⚙️ Configuration & Workflow📋 Service Level Agreements (SLAs)SLA NameGrace PeriodOperational ScopeCritical – 1 Hour1 HourSystem outages, security breaches, network downtimeHigh – 4 Hours4 HoursHardware failures, blocked access, critical applicationsNormal – 1 Business Day24 HoursGeneral software installs, routine requestsLow – 3 Business Days72 HoursNon-urgent inquiries, feature requests🏢 Departments & CategorizationAccess Requests: Account creations, permission changes, password resets.Hardware: Laptops, desktops, workstations, peripherals, printers.Network: Wi-Fi issues, corporate VPN access, routers, switches.Security: Suspicious emails, malware incidents, phishing alerts.Software & Applications: Application errors, licensing, installations.IT Support: General Tier 1 assistance.💾 Backup & Disaster RecoveryAn automated disaster recovery workflow is included via backup-osticket.ps1. It dynamically triggers mariadb-dump from inside the MariaDB container without exposing database passwords in plain text or hardcoding scripts.Manual Backup Executionpowershell -ExecutionPolicy Bypass -File .\backup-osticket.ps1
Automated Scheduling via PowerShellRun PowerShell as Administrator to schedule a daily task at 9:00 PM with automatic 14-day retention:$action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument '-ExecutionPolicy Bypass -File "C:\osticket-docker\backup-osticket.ps1"'
$trigger = New-ScheduledTaskTrigger -Daily -At 9PM
$settings = New-ScheduledTaskSettingsSet -StartWhenAvailable
Register-ScheduledTask -TaskName "osTicket Daily Database Backup" -Action $action -Trigger $trigger -Settings $settings -Description "Daily backup of the osTicket MariaDB database"
🖼️ Screenshots🎟️ Open Ticket Queue & Management👔 Staff & Agent Directory (RBAC)🏢 Department Configuration🏷️ Help Topics & Automated Ticket Categorization⏱️ Service Level Agreement (SLA) Matrix📄 Operational Runbook & Commands# Stop stack preserving database state
docker compose down

# Restart application containers
docker compose restart osticket

# Monitor live logs
docker compose logs -f --tail=100 osticket

# Execute database backup manually
docker exec osticket-db sh -c 'mariadb-dump -h 127.0.0.1 -u osticket -p"$MARIADB_PASSWORD" osticket' > backup.sql
👤 AuthorBibinRole: Technical Support & Infrastructure SpecialistPortfolio: GitHub Profile
