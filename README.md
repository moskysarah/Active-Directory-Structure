# 🛡️ Déploiement et Configuration d'Active Directory Domain Services (AD DS)

Ce dépôt contient la documentation, les architectures de référence et les scripts de configuration pour le déploiement d'une infrastructure **Active Directory** sécurisée et évolutive.

---

## 📌 Table des Matières
1. [Présentation du Projet](#-présentation-du-projet)
2. [Architecture Réseau & Système](#-architecture-réseau--système)
3. [Prérequis](#-prérequis)
4. [Étapes d'Installation](#-étapes-dinstallation)
5. [Structure des Unités Organisationnelles (OU)](#-structure-des-unités-organisationnelles-ou)
6. [Stratégies de Groupe (GPO) Appliquées](#-stratégies-de-groupe-gpo-appliquées)
7. [Sécurisation (Hardening)](#-sécurisation-hardening)
8. [Maintenance & Sauvegarde](#-maintenance--sauvegarde)

---

## 📖 Présentation du Projet
L'objectif de ce projet est de mettre en place une infrastructure d'identité et de gestion des accès (IAM) basée sur Windows Server. Cette configuration centralise la gestion des utilisateurs, des ordinateurs et des politiques de sécurité pour l'ensemble de l'organisation.

* **Nom de Domaine Interne :** `kadea.local` (ou `ad.entreprise.com`)
* **Version OS :** Windows Server 2022 / 2025
* **Niveau Fonctionnel du Domaine :** Windows Server 2016

---

## 🏗️ Architecture Réseau & Système

Le domaine repose sur une architecture à haute disponibilité avec deux contrôleurs de domaine (DC) assurant la réplication active et les rôles DNS/DHCP.

| Rôle Serveur | Nom d'Hôte | Adresse IP | Fonctions Principales |
| :--- | :--- | :--- | :--- |
| **DC Principal** | `DC01` | `10.0.1.10` | AD DS, DNS (Intégré), Maître FSMO |
| **DC Secondaire** | `DC02` | `10.0.1.11` | AD DS, DNS, Catalogue Global |
| **Serveur DHCP** | `DHCP01` | `10.0.1.20` | Étendues IP, Options Scope (DNS `10.0.1.10`) |

## 🗂️ Structure des Unités Organisationnelles (OU)

```text
corp.local (Racine du Domaine)
└── 🏢 CORP-ROOT
    ├── 👥 Users
    │   ├── 💼 Admin (Comptes à privilèges)
    │   ├── 🛠️ Technique
    │   └── 📈 RH-Finance
    ├── 💻 Computers
    │   ├── 🖥️ Workstations (Postes de travail)
    │   └── 📱 Laptops (Portables)
    ├── 🖨️ Devices (Imprimantes et IoT)
    └── 🛡️ Security-Groups

---

## 🛠️ Prérequis
Avant de lancer les scripts de promotion AD, assurez-vous que :
* Une **IP statique** est configurée sur chaque futur contrôleur de domaine.
* Le nom de la machine a été modifié selon la nomenclature (`DC01`, `DC02`).
* Les mises à jour Windows Server sont installées.

---

## 🚀 Étapes d'Installation

### 1. Installation du rôle via PowerShell
Exécutez la commande suivante en tant qu'Administrateur pour installer les fichiers binaires d'Active Directory :

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
