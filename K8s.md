# Kubernetes



## Glossaire

Container = en général Docker, une application et tout ce qu'il lui faut pour fonctionner (et seulement le nécessaire)

Node = VM qui héberge des pods

Pod = groupe de conteneurs

Deploiement = groupe de pods avec vérif de l'état de santé (healthcheck), redémarre les containers qui plantent.

Service =  mise en réseau, permet d'avoir un point d'entrée joignable par IP (via loadbalancer par exemple)

Minikube = Utilitaire pour dev, lance une node en local. Permet de lancer des déploiements en dev.

Kubectl = commande principale de controle de Kubernetes

Kubelet = Centre de contrôle du noeud. Il s'assure que les containers fonctionnent bien au sein des pods de la node.

Kubeadm = Permet de démarer / joindre un cluster Kubernetes (plus pour les devops)

## Minikube

Installe un Noeud K8s sur l'hyperviseur au choix (par défaut Docker mais possible de choisir)

### Pour installer un Noeud:

> minikube start 

Ajouter `--vm-driver` pour choisir l'hyperviseur

### Obtenir le Dashboard

> minikube dashboard

## Kubectl

### Obtenir la liste des pods

> kubectl get pods [-A]

### Obtenir la liste des services

> kubectl get services

### Obtenir la liste des événements

> kubectl get events

### Creer un service

Par défaut les pods ne sont pas accessible en dehors du cluster. Il faut un service pour les exposer

> kubectl expose deployment <nom du deployment> --type=LoadBalancer --port=<numéro de port>

Les fournisseur de services qui supportent le loadbalancing fournissent une IP publique. Mais sous minikube il faut la réclamer:

> minikube service <nom du service>

