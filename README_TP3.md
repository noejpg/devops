# Ansible

Ma connexion ssh marche bien :  
`
noe.jobard@tpi08:~/Documents/devops/ansible$ ssh -i id_rsa centos@noe.jobard.takima.cloud
Last login: Wed Feb  7 07:52:14 2024 from tpb11.cpe.fr
[centos@ip-10-0-1-120 ~]$ 
`

J'ai créé mon fichier de config ansible/inventories/setup.yml et la commande ansible fonctionne bien : 
```bash
noe.jobard@tpi08:~/Documents/devops/ansible$ ansible all -i inventories/setup.yml -m ping
centos@noe.jobard.takima.cloud | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```


Mon OS sur mon serveur :   
```bash
noe.jobard@tpi08:~/Documents/devops/ansible$ ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"
centos@noe.jobard.takima.cloud | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "CentOS",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "7",
        "ansible_distribution_release": "Core",
        "ansible_distribution_version": "7.9",
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false
}
```

puis suppression de httpd ...


### Documentation de l'inventaire et des commandes de base avec Ansible

#### Structure de l'inventaire

Dans ce projet Ansible, l'inventaire est organisé dans un fichier `setup.yml` situé dans le répertoire `inventories`. Voici un aperçu de la structure de l'inventaire :

```yaml
all:
  vars:
    ansible_user: centos
    ansible_ssh_private_key_file: /home/noe.jobard/Documents/devops/ansible/id_rsa
  children:
    prod:
      hosts:
        noe.jobard.takima.cloud:Check prof
- 

Reste à utiliser variable d'environnement dans 
- webserver2/simple-api-student/src/main/resources/application.yml
- ansible/roles/database/tasks/main.yml
- ...

Reste à faire déploiement

créer variable ssh private key 
```



Dans cet exemple, nous avons défini un groupe `prod` contenant un seul hôte avec son hostname ou son adresse IP.Check prof
- 

Reste à utiliser variable d'environnement dans 
- webserver2/simple-api-student/src/main/resources/application.yml
- ansible/roles/database/tasks/main.yml
- ...

Reste à faire déploiement

créer variable ssh private key 

#### Commandes de base

Voici quelques commandes de base que vous pouvez utiliser avec Ansible pour interagir avec vos serveurs :

- **Test de connectivité avec les hôtes** :

```bash
ansible all -i inventories/setup.yml -m ping
```

Cette commande teste la connectivité avec tous les hôtes définis dans l'inventaire.

- **Récupération des informations système** :

```bash
ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"
```

Cette commande récupère des informations sur la distribution du système d'exploitation de tous les hôtes.

- **Suppression d'un package** :

```bash
ansible all -i inventories/setup.yml -m yum -a "name=httpd state=absent" --become
```

Cette commande supprime le package httpd de tous les hôtes.

## Playbooks

Mon playbook fonctionne bien :   

```bash
noe.jobard@tpi08:~/Documents/devops/ansible$ ansible-playbook -i inventories/setup.yml playbook.yml

PLAY [all] *********************************************************************************************************************************************************************************

TASK [Test connection] *********************************************************************************************************************************************************************
ok: [centos@noe.jobard.takima.cloud]

PLAY RECAP *********************************************************************************************************************************************************************************
centos@noe.jobard.takima.cloud : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
Check prof
- 

Reste à utiliser variable d'environnement dans 
- webserver2/simple-api-student/src/main/resources/application.yml
- ansible/roles/database/tasks/main.yml
- ...

Reste à faire déploiement

créer variable ssh private key 
Installations
-
 ```bash
 noe.jobard@tpi08:~/Documents/devops/ansible$ ansible-playbook -i inventories/setup.yml advanced_playbook.ymlCheck prof
- 

Reste à utiliser variable d'environnement dans 
- webserver2/simple-api-student/src/main/resources/application.yml
- ansible/roles/database/tasks/main.yml
- ...

Reste à faire déploiement

créer variable ssh private key 

PLAY [all] *********************************************************************************************************************************************************************************

TASK [Install device-mapper-persistent-data] ***********************************************************************************************************************************************
changed: [centos@noe.jobard.takimAnya.cloud]

TASK [Install lvm2] ************************************************************************************************************************************************************************
changed: [centos@noe.jobard.takima.cloud]

TASK [add repo docker] *********************************************************************************************************************************************************************
changed: [centos@noe.jobard.takima.cloud]

TASK [Install Docker] **********************************************************************************************************************************************************************
changed: [centos@noe.jobard.takima.cloud]

TASK [Install python3] *********************************************************************************************************************************************************************
changed: [centos@noe.jobard.takima.cloud]

TASK [Install docker with Python 3] ********************************************************************************************************************************************************
changed: [centos@noe.jobard.takima.cloud]

TASK [Make sure Docker is running] *********************************************************************************************************************************************************
changed: [centos@noe.jobard.takima.cloud]

PLAY RECAP *********************************************************************************************************************************************************************************
centos@noe.jobard.takima.cloud : ok=7    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```

Tout est bien installé

Roles
-

Création du rôle :
```bash
noe.jobard@tpi08:~/Documents/devops/ansible$ ansible-galaxy init roles/docker
- Role roles/docker was created successfully
```

Je déplace ensuite mon install de docker dans `roles/docker/tasks/main.yml`

Puis j'appelle le rôle depuis mon playbook principal en ajoutant 
``` 
roles:
    - docker
```

Puis tout s'installe bien quand j'appelle mon playbook principal !

# Documentation des playbooks

Premier playbook
- 

- **Objectif** : Tester la connexion à tous les hôtes.
- **Emplacement** : `mon-projet/ansible/playbook.yml`
- **Tâches** :
    - Tester la connexion aux hôtes en utilisant le module `ping`.
- **Rôles** :
    - Appeler le rôle docker pour l'installation.

Playbook avancé
- 

- **Objectif** : Installer Docker sur tous les hôtes.
- **Tâches** :
    - Installer les paquets requis pour l'installation de Docker.
    - Ajouter le dépôt Docker.
    - Installer Docker CE.
    - Installer Python 3.
    - Installer la bibliothèque Docker Python en utilisant pip3.
    - S'assurer que le service Docker est en cours d'exécutiCheck prof
- 

Reste à utiliser variable d'environnement dans 
- webserver2/simple-api-student/src/main/resources/application.yml
- ansible/roles/database/tasks/main.yml
- ...

Reste à faire déploiement

créer variable ssh private key on.


## Deploy your app

Création des rôles :
```bash
noe.jobard@tpi08:~/Documents/devops/ansible$  
- Role roles/create_network was created successfully
noe.jobard@tpi08:~/Documents/devops/ansible$ ansible-galaxy init roles/database
- Role roles/launch_database was created successfully
noe.jobard@tpi08:~/Documents/devops/ansible$ ansible-galaxy init roles/app
- Role roles/launch_app was created successfully
noe.jobard@tpi08:~/Documents/devops/ansible$ ansible-galaxy init roles/proxy
- Role roles/launch_proxy was created successfully
```


Configuration des Tâches docker_container
- 
Dans cette tâche, nous lançons le conteneur Docker de l'application avec les paramètres suivants :

- name: Le nom du conteneur est défini sur la valeur de la variable app_name.
- image: L'image Docker à utiliser est spécifiée par la variable docker_image.
- state: Nous définissons l'état du conteneur comme "started", ce qui signifie que nous voulons que le conteneur soit démarré.
    networks: Le conteneur est connecté au réseau spécifié par la variable network.
- env: Nous définissons les variables requises pour l'application, telles que POSTGRES_DB, POSTGRES_USER et POSTGRES_PASSWORD, en utilisant les valeurs des variables d'environnement définies dans le fichier setup.yml.

Les variables (entre accolades) tels que {{ docker_image }} sont définies dans le fichier vars du rôle, on peut donc garder un code presque similaire entres les tasks des rôles

Cette configuration assure le déploiement et le fonctionnement correct du conteneur Docker de l'application avec les paramètres nécessaires => Le site fonctionne parfaitement bien après cela !


Front
- 

- Ajout au dockercomposer
- Ajout au workflow pour le push sur docker hub
- Création d'un nouveau rôle pour le récupérer
- Changement dans le httpd.conf pour repasserpar le proxy avec /api


Déploiement continu
- 

- J'ai mis ma clé privée dans une variable Github
- J'ai rajouté la déploiement à mon workflow Gitgub$


=> Tout fonctionne bien et le déploiement se fait bien