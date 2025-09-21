# Greenshop
versions utilisées : Mariadb  : 10.6.21
// PHP :  8.1.2 -1ubuntu 2.21
// Apache2: 2.4.52
// OS Ubuntu: 22.04.5 LTS

# 🛒 Greenshop – Infrastructure as Code (IaC) sur AWS

## 📘 Description du projet
Greenshop est une infrastructure complète déployée sur AWS dans une approche **Infrastructure as Code (IaC)**.  
Initialement réalisé en **une semaine dans le cadre d’un hackaton**, le projet a ensuite été enrichi et finalisé pour servir de **support à la validation du Bachelor Administrateur Réseau et Sécurité Opérationnelle**.

L’infrastructure combine **Terraform**, **Ansible**, **Docker**, **Jenkins**, **HAProxy**, **Prometheus** et **Grafana** pour automatiser le déploiement, la mise à jour et la supervision d’une application web LAMP connectée à une base de données **MariaDB**.

---

## 🧱 Architecture
L’architecture réseau repose sur un **VPC personnalisé** et trois sous-réseaux :

| Sous-réseau          | Usage                              |
|----------------------|-----------------------------------|
| `192.168.1.0/24`     | Public : Bastion, Load Balancer HAProxy, Jenkins |
| `192.168.10.0/24`    | Privé App : 3 serveurs Docker web |
| `192.168.20.0/24`    | Privé DB : MariaDB |

### Ressources déployées
- 🌐 **1 Load Balancer HAProxy** (via VM EC2 publique)  
- 🔐 **1 Bastion SSH**  
- 🔧 **1 Jenkins** (CI/CD via webhook GitHub)  
- 🖥 **3 serveurs web Docker** (Apache + PHP + Greenshop)  
- 🗄 **1 MariaDB** (dump importé depuis GitHub)  
- 📊 **1 Prometheus + Grafana** (supervision et dashboards)

---

## ⚙️ Déroulement et outils utilisés

### 1. **Terraform – Provisionnement AWS**
- Création automatique du VPC, sous-réseaux, Security Groups et instances EC2.
- Intégration du Load Balancer HAProxy directement dans Terraform.

### 2. **Ansible – Configuration automatisée**
- Déploiement et sécurisation de MariaDB avec récupération automatique du `init.sql`.
- Installation et configuration d’HAProxy (round-robin entre les trois serveurs web).
- Provisionnement des serveurs Docker et déploiement de l’application Greenshop.
- Installation de Prometheus et Grafana + exporters (Node Exporter sur chaque VM, mysqld-exporter sur MariaDB).

### 3. **Jenkins – Intégration et déploiement continu**
- Webhook GitHub redirigé via HAProxy pour déclencher Jenkins depuis un réseau privé.
- Build d’une nouvelle image Docker Greenshop à chaque commit.
- Push sur Docker Hub et mise à jour automatisée des conteneurs sur les trois serveurs web.

### 4. **Prometheus & Grafana – Supervision**
- Prometheus collecte les métriques système et applicatives.
- Dashboards Grafana pour :
  - Temps de réponse des serveurs web.
  - État des conteneurs et de MariaDB.
  - Vérification du statut des pipelines Jenkins.

---

## 🛠 Technologies utilisées

| Outil        | Usage principal                          |
|---------------|-----------------------------------------|
| Terraform     | Création des ressources AWS              |
| Ansible       | Provisionnement & configuration système  |
| Docker        | Conteneurisation des applications        |
| Jenkins       | CI/CD automatisé via webhook GitHub      |
| HAProxy       | Load balancing HTTP                      |
| Prometheus    | Collecte de métriques                    |
| Grafana       | Visualisation et alerting                |
| MariaDB       | Base de données relationnelle            |
| Ubuntu 22.04  | OS pour toutes les VM                    |

---

## 🚀 Déploiement

### Prérequis
- Créez un compte AWS et configurez vos credentials :  
  ```bash
  export AWS_ACCESS_KEY_ID=...
  export AWS_SECRET_ACCESS_KEY=...

- Modifiez le fichier Terraform pour autoriser votre IP publique dans cidr_blocks.

Étapes

### Provisionnement AWS (Terraform)
  Créer l'instance :
  ```bash
  cd greenshop-terraform
  terraform init
  terraform apply
  ```

### Configuration automatisée (Ansible)
  ```bash
  cd greenshop-ansible
  ansible-playbook setup.yml -i inventory.ini
  ```

### CI/CD (Jenkins)

Accédez à Jenkins via l’IP publique du Load Balancer (port 8080 redirigé).

Configurez votre pipeline (freestyle ou pipeline-as-code).

Ajoutez le webhook GitHub : http://<LOADBALANCER_PUBLIC_IP>:8080/github-webhook/.

### Supervision (Grafana)

Grafana accessible via http://<LOADBALANCER_PUBLIC_IP>:3000.

Dashboards préconfigurés pour les métriques système et applicatives

### 📎 Notes et Limitations techniques

- AWS Student : Pas d’accès à IAM ni RDS → authentification et DB gérées manuellement.
- Load Balancer : Utilisation d’HAProxy sur EC2 au lieu d’un ELB managé.
- Sécurité : Jenkins placé derrière HAProxy pour exposer uniquement les endpoints nécessaires au webhook.
- Initial hackaton : Version rendue fonctionnelle mais sans supervision ni webhook ; ces éléments ont été ajoutés après coup pour un environnement complet.
- Preuve pédagogique : Projet destiné à démontrer une approche DevOps/IaC et non à remplacer une architecture de production réelle.

### 👨‍🎓 Contexte pédagogique

Projet initialement réalisé en hackaton d’une semaine (4 étudiants, terminé par 2) puis amélioré et finalisé pour la validation du Bachelor.
Objectifs pédagogiques :

- Mettre en pratique l’IaC et les bonnes pratiques DevOps.
- Automatiser le déploiement et la supervision d’une application LAMP.
- Intégrer CI/CD et monitoring dans une architecture modulaire.

### 📧 Auteur

Berzylyss
GitHub: https://github.com/Berzylyss
