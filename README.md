# WorkSphere  
## Azure Platform Engineering Projekt

---

## Überblick

Dieses Repository dokumentiert den Aufbau einer produktionsnahen Azure-Plattform für das fiktive B2B-SaaS-Unternehmen **WorkSphere**.

Ziel des Projekts war es, eine sichere, skalierbare und standardisierte Cloud-Grundlage zu entwickeln, die als Basis für zukünftige Applikations-Deployments dient.

Der Fokus liegt bewusst auf:

- Cloud-Architektur  
- Security by Design  
- Governance  
- Infrastructure as Code (Terraform)  

---

## Zielsetzung

Die Plattform sollte:

- Entwicklungs- und Produktionsumgebungen strikt trennen  
- Sensible Ressourcen nicht öffentlich exponieren  
- Secrets sicher verwalten  
- Teamfähig über Remote State betrieben werden  
- Governance-Standards einhalten  
- Vollständig reproduzierbar per Terraform bereitgestellt werden  

Dieses Projekt demonstriert meine Fähigkeiten im Bereich:

- Azure Cloud Engineering  
- Plattformarchitektur  
- Terraform & Modularisierung  
- RBAC & Identity Design  
- Private Networking  
- Enterprise Governance  

---

# Architektur

## High-Level Architekturdiagramm

```mermaid
flowchart LR
    subgraph Dev
        VNET_DEV[VNet Dev]
        APP_DEV[App Subnet]
        PE_DEV[Private Endpoint Subnet]
        KV_DEV[Key Vault]
        SQL_DEV[Azure SQL]

        VNET_DEV --> APP_DEV
        VNET_DEV --> PE_DEV
        PE_DEV --> KV_DEV
        PE_DEV --> SQL_DEV
    end

    subgraph Prod
        VNET_PROD[VNet Prod]
        APP_PROD[App Subnet]
        PE_PROD[Private Endpoint Subnet]
        KV_PROD[Key Vault]
        SQL_PROD[Azure SQL]

        VNET_PROD --> APP_PROD
        VNET_PROD --> PE_PROD
        PE_PROD --> KV_PROD
        PE_PROD --> SQL_PROD
    end

    TF[Terraform]
    STATE[Azure Storage - Remote State]

    TF --> Dev
    TF --> Prod
    TF --> STATE
