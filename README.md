# WorkSphere – Azure Platform Engineering Projekt

## 📌 Projektübersicht

Dieses Repository dokumentiert den Aufbau einer professionellen Azure-Plattform für ein fiktives B2B SaaS-Unternehmen **WorkSphere**.

WorkSphere entwickelt eine Workflow- und Operations-Management Plattform für mittelständische Unternehmen.  
Ziel dieses Projekts ist es, eine sichere, standardisierte und reproduzierbare Azure-Infrastruktur aufzubauen, die als Grundlage für zukünftige Applikations-Deployments dient.

Der Fokus liegt auf:

- Infrastructure as Code (Terraform)
- Dev/Prod Trennung
- Private Networking
- Security & RBAC
- Remote State Management
- Plattform-Governance

---

## 🏗 Architekturüberblick

Die Plattform besteht aus folgenden Kernkomponenten:

### 🌐 Networking
- Virtual Network pro Environment (Dev / Prod)
- Separate Subnets:
  - Application Subnet
  - Private Endpoint Subnet
- Network Security Group am Application Subnet
- Private DNS Zones für:
  - Key Vault
  - Azure SQL
- Private Endpoints für:
  - Azure Key Vault
  - Azure SQL Server

### 🔐 Security
- Azure Key Vault mit RBAC aktiviert
- User Assigned Managed Identity für Applikationen
- Rollen:
  - Key Vault Secrets User
  - Key Vault Administrator (Bootstrap)
- SQL Server mit Azure AD Administrator
- Public Network Access für SQL in Prod deaktiviert

### 🗄 Datenbank
- Azure SQL Server (Dev & Prod getrennt)
- Private Connectivity via Private Endpoint
- SQL Admin Passwort wird in Key Vault gespeichert
- Azure AD Integration

### 🏷 Governance
Naming Convention: <resource-type>-<environment>-<region>-<workload>-<instance>


Beispiel:


rg-prod-we-platform-01
vnet-dev-we-core-01


Tags (konsequent auf allen Ressourcen):

- Environment
- Owner
- CostCenter
- Project
- Criticality

---

## 🌍 Dev vs. Prod – Unterschiede

| Bereich | Dev | Prod |
|----------|------|-------|
| Remote State Key | dev.terraform.tfstate | prod.terraform.tfstate |
| SQL SKU | Basic | S0 |
| Key Vault Retention | 7 Tage | 30 Tage |
| Purge Protection | deaktiviert | aktiviert |
| Criticality Tag | medium | high |
| Netzwerkbereich | 10.10.0.0/16 | 10.20.0.0/16 |

Beide Umgebungen verwenden dieselben Terraform-Module, sind jedoch logisch und infrastrukturell getrennt.

---

## 📦 Terraform Struktur


infra/
bootstrap/ → Provisioniert Remote State Storage
dev/ → Dev Umgebung
prod/ → Prod Umgebung
modules/
network/ → Wiederverwendbares Netzwerkmodul


---

## 🔄 Remote State & Bootstrap

Für den produktionsnahen Betrieb wurde ein separater Bootstrap-Stack erstellt.

Dieser provisioniert:

- Azure Storage Account
- Blob Container (tfstate)
- Versioning für State-Dateien

Dev und Prod verwenden getrennte State-Dateien:

- dev.terraform.tfstate
- prod.terraform.tfstate

Vorteile:

- Zentrale State-Verwaltung
- State Locking
- Teamfähigkeit
- Versionierung & Recovery

---

## 🔐 Sicherheitsprinzipien

- Private Endpoints statt Public Exposure
- RBAC statt Access Policies
- Trennung von Dev und Prod
- Secrets ausschließlich im Key Vault
- Public Network Access für SQL in Prod deaktiviert
- Azure AD als Identitätsbasis

---

## 🧠 Architekturentscheidungen (Kurzfassung)

- Einsatz von Terraform-Modulen zur Wiederverwendbarkeit
- Remote State für Teamfähigkeit
- Private Networking als Default
- Environment-Isolation über getrennte State Keys
- RBAC-first Strategie für Azure Services

Eine detaillierte Dokumentation der Entscheidungen befindet sich im Ordner:


docs/decisions/


---

## 📸 Architektur- und Implementierungsnachweise

### 🔹 Dev Umgebung – Ressourcenübersicht

![Dev Ressourcen](docs/screenshots/dev-ressources.png)

---

### 🔹 Key Vault – RBAC & Identity Modell (Dev)

![Key Vault IAM](docs/screenshots/Kv-iam-png.png)

---

### 🔹 Key Vault – Networking (Prod)

![Key Vault Netzwerk Prod](docs/screenshots/kv-prod-networking.png)

---

### 🔹 Key Vault – Private Endpoint (Prod)

![Key Vault Private Endpoint](docs/screenshots/kv-prod-privateendpoint.png)

---

### 🔹 SQL Server – Public Access deaktiviert

![SQL Public Access Off](docs/screenshots/Sql-server-publicaccess-off.png)

---

### 🔹 SQL Server – Private Endpoint

![SQL Private Endpoint](docs/screenshots/Sql-server-privateendpoint.png)

---

### 🔹 Prod Umgebung – Ressourcenübersicht

![Prod Ressourcen](docs/screenshots/prod-ressources.png)

---

### 🔹 Remote Terraform State (Azure Storage Backend)

![Terraform State Container](docs/screenshots/tf-state-container.png)
