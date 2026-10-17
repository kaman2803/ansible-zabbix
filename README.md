# Ansible Zabbix Deployment

## 1. Présentation

Ce projet automatise le déploiement de **Zabbix 7.4 sur Ubuntu Server 24.04 LTS** à l'aide d'Ansible.

L'objectif est de rendre l'installation reproductible, de centraliser la configuration et de protéger les informations sensibles avec Ansible Vault.

## 2. Architecture du projet

```text
ansible-zabbix/
├── ansible.cfg
├── inventory/
│   ├── hosts
│   ├── group_vars/
│   │   └── zabbix/
│   │       ├── main.yml
│   │       └── vault.yml
│   └── host_vars/
├── playbooks/
│   ├── 01-diagnostic.yml
│   ├── 02-preparation-systeme.yml
│   ├── 03-base-donnees.yml
│   ├── 04-configuration-zabbix.yml
│   └── 05-configuration-frontend.yml
├── roles/
└── templates/
    └── zabbix.conf.php.j2
```

## 3. Technologies utilisées

- Ubuntu Server 24.04 LTS
- Ansible Core 2.16.3
- Zabbix 7.4
- Apache HTTP Server
- MariaDB
- PHP 8.3
- Ansible Vault
- Jinja2

## 4. Déploiement automatisé

Les playbooks sont organisés par étapes :

1. Diagnostic de la machine cible.
2. Préparation du système et installation des prérequis.
3. Création et initialisation de la base de données Zabbix.
4. Configuration et démarrage du serveur Zabbix.
5. Configuration et vérification du frontend Web.

Avant l'exécution, vérifier l'inventaire, les variables et l'accès SSH à la machine cible.

```bash
ansible zabbix -m ping
```

Vérifier la syntaxe d'un playbook :

```bash
ansible-playbook --syntax-check playbooks/01-diagnostic.yml
```

Exécuter les playbooks dans l'ordre, en adaptant les commandes aux variables et aux secrets configurés :

```bash
ansible-playbook playbooks/01-diagnostic.yml --ask-vault-pass
ansible-playbook playbooks/02-preparation-systeme.yml --ask-vault-pass
ansible-playbook playbooks/03-base-donnees.yml --ask-vault-pass
ansible-playbook playbooks/04-configuration-zabbix.yml --ask-vault-pass
ansible-playbook playbooks/05-configuration-frontend.yml --ask-vault-pass
```

## 5. Sécurité

Les informations sensibles sont stockées dans `inventory/group_vars/zabbix/vault.yml`, chiffré avec Ansible Vault.

**Ne jamais publier de mots de passe en clair, de clés privées SSH ou de fichiers contenant des secrets.** Vérifier que le fichier Vault est bien chiffré avant de publier le dépôt.

## 6. Validation

Les vérifications réalisées au cours du projet comprennent :

- Connectivité SSH et réponse Ansible Ping.
- Installation et état des services.
- Initialisation de la base de données Zabbix.
- Configuration du serveur et écoute sur le port TCP 10051.
- Syntaxe PHP et disponibilité HTTP du frontend.
- Exécution des playbooks sans changement inutile lorsque la configuration est déjà conforme.

Le déploiement a été validé sur une machine Ubuntu Server 24.04 LTS. Le comportement de chaque playbook doit être vérifié avant toute réutilisation sur une nouvelle machine.
