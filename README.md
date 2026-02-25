# Conception-et-D-ploiement-d-une-Plateforme-SIEM-Open-Source
# Wazuh + Suricata – Détection d’Intrusions en Conditions Réelles
# Résumé Exécutif

Ce projet présente la conception, le déploiement et l’évaluation d’une plateforme SIEM open source basée sur Wazuh, intégrée à l’IDS réseau Suricata, dans un environnement virtualisé reproduisant une infrastructure d’entreprise.

L’objectif principal est de démontrer la capacité d’un SIEM open source à :

- Collecter des logs multi-sources
- Détecter des attaques réseau, système et applicatives
- Corréler les événements de sécurité
- Générer des alertes exploitables en temps réel
- Fournir une visibilité complète sur la chaîne d’attaque

L’environnement inclut :

- Un serveur Wazuh (Manager, Indexer, Dashboard)
- Une machine Kali Linux (attaquant)
- Une machine Ubuntu (DVWA + Suricata)
- Une machine Windows 10 (victime)

# Architecture de la Plateforme
## Composants principaux

La plateforme repose sur l’architecture modulaire de Wazuh 
D_ploiement_d_une_Plateforme_SI… :

- Wazuh Manager : collecte et corrélation des événements
- Wazuh Agents : déployés sur Ubuntu et Windows
- Wazuh Indexer (OpenSearch/Elasticsearch) : stockage et indexation
- Wazuh Dashboard : visualisation centralisée
- Suricata IDS : détection réseau (logs JSON eve.json)

## Flux de supervision

 1. Suricata analyse le trafic réseau.
 2. Les logs sont générés dans /var/log/suricata/eve.json.
 3. L’agent Wazuh collecte ces logs.
 4. Le Manager applique les règles de détection.
 5. Les alertes sont indexées et affichées dans le Dashboard.

Cette architecture permet une corrélation multi-sources (réseau + système + application).

# Environnement Technique
## Serveur SIEM

- Ubuntu Server
- Wazuh (Manager, Indexer, Dashboard)
- Port 443 activé pour accès sécurisé
- Accès HTTPS sécurisé

## Machine Attaquante

- Kali Linux
- Outils : Nmap, Hydra
- Tests réseau et applicatifs

## Machine Victime 1

- Windows 10 Pro
- Agent Wazuh installé
- Surveillance des logs Windows

## Machine Victime 2

- Ubuntu
- Apache2 + MariaDB
- DVWA (Damn Vulnerable Web Application)
- Suricata IDS
- Agent Wazuh installé

# Déploiement Technique
## 1. Installation du Serveur Wazuh

  - Configuration des nœuds via config.yml
  - Installation de l’Indexer
  - Installation du Manager
  - Installation du Dashboard
  - Génération des certificats
  - Initialisation du cluster

Vérification :

- Accès HTTPS au Dashboard
- Connexion administrateur
- Vérification API

## 2. Déploiement des Agents
## Agent Windows

- Installation MSI
- Configuration du Manager
- Vérification remontée des logs

## Agent Ubuntu

- Installation via dépôt officiel
- Configuration dans /var/ossec/etc/ossec.conf
- Intégration Suricata (lecture eve.json)

# 3. Intégration Suricata

- Installation via dépôt OISF
- Activation mode IDS
- Configuration interface réseau
- Vérification des logs JSON
- Intégration dans Wazuh via module localfile

Groupes de règles activés :

- suricata
- ids
- network

# Simulation d’Attaques
## 1. Reconnaissance Réseau (Nmap)

- Scan SYN furtif
- Scan de services

Détection :

- Alertes ET SCAN Nmap
- Corrélation Suricata → Wazuh

# 2. Attaques Brute-Force

## SSH (Ubuntu)
- Hydra → tentative brute-force SSH

Alertes détectées :

- SSH Authentication Failure
- ET SCAN/BRUTE FORCE SSH
- Authentications failures count exceeded

## RDP (Windows)

- Hydra → brute-force RDP

Alertes détectées :

- ET POLICY RDP brute force

# 3. Attaques Applicatives (DVWA)
## SQL Injection

Détection :

- Signatures Suricata HTTP SQL Injection
- Règles Wazuh corrélées

## Cross-Site Scripting (XSS)

Détection :

- Suricata HTTP XSS Attempt
- Alertes IDS consolidées

## Command Injection

Détection :

- Anomalies HTTP
- Corrélation logs Apache + Suricata

## Brute-Force Login DVWA

Détection :

- HTTP brute-force
- Corrélation multi-alertes

# Corrélation Wazuh – Suricata
## Suricata

- Détection au niveau réseau (paquets, signatures)
- Très rapide
- Basée sur signatures

## Wazuh

- Corrélation multi-source
- Ajout de contexte système
- Consolidation des phases d’attaque
- Vision complète : reconnaissance → exploitation → post-exploitation

La complémentarité IDS + SIEM permet :

- Réduction des angles morts
- Détection contextualisée
- Meilleure compréhension des incidents

# Résultats & Analyse
## Attaques correctement détectées

- Scans Nmap
- Brute-force SSH/RDP
- SQL Injection
- XSS
- Command Injection
- Brute-force HTTP

## Faux positifs observés

- Certaines requêtes HTTP légitimes
- Alertes Suricata trop sensibles lors de scans intensifs

## Ajustements réalisés

- Adaptation des règles locales Wazuh
- Ajustement signatures Suricata
- Filtrage IP internes
- Augmentation seuils brute-force

# Problèmes Techniques Rencontrés
## 1. Saturation disque

Cause :

- Volume élevé de logs indexés

Solution :

- Extension disque VirtualBox
- Redimensionnement LVM
- Vérification via df -h

## 2. Incompatibilité API Wazuh

Erreur :

- API version mismatch
- 403 / invalid credentials

Solution :

- Vérification versions
- Mise à jour-  homogène via script officiel
- Redémarrage service- s

## 3. Non remontée logs Suricata

Vérifications :

- Présence eve.json
- Permissions
- Configuration ossec.conf
- Redémarrage agent

# Évaluation de l’Efficacité
## Points forts

- Détection multi-couches (réseau + système + application)
- Alertes en temps réel
- Corrélation automatique
- Dashboard centralisé
Solution open source complète

## Limites

- Forte consommation disque indexer
- Suricata très verbeux
- Configuration fine nécessaire pour réduire faux positifs

# Perspectives d’Amélioration

- Cluster multi-nœuds Wazuh
- Architecture Suricata distribuée
- Pipeline SIEM–SOAR
- Archivage et rotation avancée des logs
- Monitoring 24/7 automatisé
- Déploiement en environnement production

# Compétences Démontrées

- Conception d’architecture SIEM
- Déploiement Wazuh complet (Manager, Indexer, Dashboard)
- Intégration IDS réseau (Suricata)
- Analyse et corrélation d’événements
- Simulation d’attaques réelles
- Troubleshooting avancé
- Optimisation règles et réduction faux positifs
- Vision SOC / Blue Team

# Positionnement Professionnel

Ce projet démontre des compétences opérationnelles en :
- SOC Analyst
- Blue Team
- SIEM Engineer
- Security Monitoring
- Threat Detection & Correlation
- Architecture Open Source Security

## Auteur
Karadag Nissa
