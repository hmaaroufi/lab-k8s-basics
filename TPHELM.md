# 🧪 TP de Prise en Main de Helm avec Kubernetes

Ce lab vous guide pas à pas dans la découverte de **Helm**, le gestionnaire de packages pour Kubernetes. Helm simplifie le déploiement et la gestion d'applications complexes sur Kubernetes.

![Helm Logo](./images/helm.png)

---

## ⚙️ Prérequis

Avant de commencer, assurez-vous que Minikube et Helm sont installés :

```
minikube start
helm version
```

Si Helm n'est pas installé :
```
# Windows (avec Chocolatey)
choco install kubernetes-helm

# Ou télécharger depuis https://helm.sh/docs/intro/install/
```

## Installation de Helm (si nécessaire)
Vérifier l'installation :
```
helm version --short
```

## Ajout d'un repository Helm public
Ajouter le repository officiel Bitnami :
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```
Mettre à jour les repositories :
```
helm repo update
```
Lister les repositories :
```
helm repo list
```

## Recherche de charts disponibles
Rechercher des charts nginx :
```
helm search repo nginx
```
Rechercher dans le hub Helm :
```
helm search hub nginx
```

## Installation simple d'une application avec Helm
Installer nginx avec Helm :
```
helm install my-nginx bitnami/nginx
```
Vérifier l'installation :
```
helm list
kubectl get pods
kubectl get svc
```

## Inspection d'une release Helm
Voir les détails de la release :
```
helm status my-nginx
```
Voir l'historique :
```
helm history my-nginx
```
Voir les valeurs utilisées :
```
helm get values my-nginx
```

## Suppression d'une release
Supprimer la release :
```
helm uninstall my-nginx
```
Vérifier la suppression :
```
helm list
kubectl get pods
```

## Création d'un Chart Helm personnalisé
Créer un nouveau chart :
```
helm create my-webapp
```
Explorer la structure créée :
```
ls my-webapp/
```
Structure du chart :
```
my-webapp/
├── Chart.yaml          # Métadonnées du chart
├── values.yaml          # Valeurs par défaut
├── templates/           # Templates Kubernetes
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   └── _helpers.tpl
└── charts/             # Dépendances
```

## Personnalisation du Chart
Modifier le fichier `my-webapp/values.yaml` :
```yaml
replicaCount: 2

image:
  repository: nginx
  tag: "1.21"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
```

## Installation du Chart personnalisé
Installer depuis le répertoire local :
```
helm install my-custom-app ./my-webapp
```
Vérifier :
```
helm list
kubectl get pods
kubectl get svc
```

## Mise à jour avec de nouvelles valeurs
Modifier la valeur de répliques directement :
```
helm upgrade my-custom-app ./my-webapp --set replicaCount=3
```
Ou avec un fichier de valeurs personnalisé :
```
# Créer custom-values.yaml
echo "replicaCount: 4" > custom-values.yaml
helm upgrade my-custom-app ./my-webapp -f custom-values.yaml
```
Vérifier la mise à jour :
```
kubectl get pods
helm history my-custom-app
```

## Rollback d'une release
Faire un rollback vers la version précédente :
```
helm rollback my-custom-app 1
```
Vérifier :
```
kubectl get pods
helm history my-custom-app
```

## Test et validation du Chart
Effectuer un dry-run pour tester :
```
helm install test-release ./my-webapp --dry-run --debug
```
Valider la syntaxe du chart :
```
helm lint ./my-webapp
```
Tester les templates :
```
helm template my-test ./my-webapp
```

## Empaquetage du Chart
Empaqueter le chart en archive :
```
helm package ./my-webapp
```
Vérifier le package créé :
```
ls *.tgz
```

## Installation depuis un package local
Installer depuis l'archive :
```
helm install packaged-app ./my-webapp-0.1.0.tgz
```
Vérifier :
```
helm list
```

## Gestion des dépendances
Modifier `my-webapp/Chart.yaml` pour ajouter une dépendance :
```yaml
dependencies:
  - name: redis
    version: "17.x.x"
    repository: "https://charts.bitnami.com/bitnami"
```
Télécharger les dépendances :
```
helm dependency update ./my-webapp
```
Vérifier :
```
ls my-webapp/charts/
```

## Utilisation de Helm avec des namespaces
Créer un namespace :
```
kubectl create namespace production
```
Installer dans un namespace spécifique :
```
helm install prod-app ./my-webapp --namespace production
```
Lister par namespace :
```
helm list --namespace production
helm list --all-namespaces
```

## Débogage et troubleshooting
Voir les ressources générées :
```
helm get manifest my-custom-app
```
Voir tous les détails :
```
helm get all my-custom-app
```
Historique complet :
```
helm history my-custom-app --max 10
```

## Nettoyage final
Supprimer toutes les releases :
```
helm uninstall my-custom-app
helm uninstall packaged-app
helm uninstall prod-app --namespace production
```
Supprimer le namespace :
```
kubectl delete namespace production
```
Supprimer les fichiers créés :
```
rm -rf my-webapp/
rm *.tgz
rm custom-values.yaml
```

---

## 📋 Résumé des concepts Helm

| Concept | Description | Commande principale |
|---------|-------------|-------------------|
| **Chart** | Package d'application Kubernetes | `helm create` |
| **Release** | Instance déployée d'un chart | `helm install` |
| **Repository** | Registre de charts | `helm repo add` |
| **Values** | Configuration du chart | `values.yaml`, `--set` |
| **Template** | Fichiers Kubernetes avec templating | `templates/` |
| **Upgrade** | Mise à jour d'une release | `helm upgrade` |
| **Rollback** | Retour version précédente | `helm rollback` |

## 🎯 Points clés à retenir

1. **Helm = Package Manager** pour Kubernetes
2. **Charts** = Templates réutilisables d'applications
3. **Values** = Configuration flexible et réutilisable  
4. **Releases** = Instances trackées et versionnées
5. **Repositories** = Distribution centralisée de charts
6. **Templating** = Génération dynamique de manifests Kubernetes

Ce TP vous donne les bases pour utiliser Helm efficacement dans vos projets Kubernetes !

