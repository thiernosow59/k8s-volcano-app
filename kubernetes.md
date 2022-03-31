# Kubernetes

Kubernetes est un orchestrateur de conteneur (pas forcément Docker).

Il permet de gérer un cluster Kubernetes dans lequel un executeur de conteneur est installé.

## Kubectl

Kubectl permet d'envoyer des commandes au cluster.

### Lire les ressources

`kubectl get <ressource>`

Pour obtenir la liste des pods:
`kubectl get pods`

Pour obtenir la liste des services: `kubectl get services`

### Créer une ressource

En ligne de commande: `kubectl create <ressource> [options de la ressource] <nom de la ressource>`

Créer un déploiement de pod nginx: `kubectl create pod --image=nginx web-server`

Créer une ressource à partir de fichier(s): `kubectl apply -f <chemin du fichier ou dossier>`

## Décrire une ressource

il y a deux façons de décrire une ressource:

`kubectl describe <type de ressource> <nom de ressource>`

`kubectl describe <nom complet de la ressource>`

Par exemple:
`kubectl describe pod volcano-api-546321zsdf`

`kubectl describe deployment.apps/volcano-api`

## Récupérer les logs d'un pod

`kubectl logs pod/volcano-api-7cdf8cc5db-mdq5h`

Cette commande récupére la sortie console du container du pod.

## Les Manifestes

Les manifestes sont des fichiers au format YML, YAML ou JSON

Ce fichier est normalisé et sa structure dépend de la version de l'API

Exemple:

```yml
# version de l'API (pour vérifier la structure du yml)
apiVersion: apps/v1
# type de la ressource
kind: Deployment
metadata:
# nom de la ressource
  name: volcano-api
# spécifications de la ressource
spec:
  # nombre de pods à déployer
  replicas: 2
  # selection des pods gérés par le deployment
  selector:
    matchLabels:
      app: volcano-api
      side: back
  # modèle de pod à déployer
  template:
    metadata:
      labels:
        app: volcano-api
        side: back
    # spécification du pod
    spec:
      containers:
      - name: volcano-api
        image: benoitsemifir/volcano-api
        resources:
        # besoin de ressources
          requests:
            memory: "250Mi"
            cpu: "500m"
        # limite à ne pas dépasser (sinon K8S)
          limits:
            memory: "500Mi"
            cpu: "1000m"
        ports:
        - containerPort: 3000
        # Les variables d'environnement
        env:
          - name: MONGO_DB
            value: volcanoapp
          - name: MONGO_PORT
            value: "27017"
          - name: MONGO_URL
            value: mongo
---
apiVersion: v1
kind: Service
metadata:
  name: volcano-api
spec:
  # 3 types de services:
  # - ClusterIp: pour la redirection interne, back vers bdd
  # - NodePort:  pour atteindre un noeud depuis l'exterieur
  # - LoadBalancer: pour atteindre un pod depuis l'exterieur
  type: LoadBalancer
#   quels sont les ressources ciblée par le service
  selector:
    app: volcano-api
    side: back
  ports:
  # port du service (externe)
  - port: 3000
#   pour de la ressource (interne)
    targetPort: 3000
```

## Kompose

Kompose est un outil qui permet de convertir un docker-compose.yml en fichiers kubernetes.

### Installer Kompose

[Installation](https://kompose.io/installation/)

Sous Windows: `choco install kubernetes-kompose`

Sous MacOS: `brew install kompose`

Sous linux: ça dépend des distro

### Convertir un docker-compose

Dans un CLI à l'emplacement du docker-compose.yml: `kompose convert`

Si vous souhaitez obtenir les manifestes K8s dans un autre dossier, placez-vous dans le dossier et entrez la commande: `kompose convert -f <chemin vers le docker compose>`

La conversion est rarement parfaite, pensez à la vérifier et à la modifier le cas échéant (en général pour le type d'un service).
