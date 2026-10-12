# TP Infrastructure as Code — Terraform et Docker

Déploiement d'un serveur Nginx dans un conteneur Docker avec Terraform, en local.

## Contenu du projet

| Fichier | Rôle |
|---|---|
| `main.tf` | Provider Docker, image Nginx, réseau et conteneur |
| `variables.tf` | Variables `container_name` et `external_port` |
| `outputs.tf` | Nom du conteneur, nom du réseau et URL du site |
| `html/index.html` | Page Web servie par Nginx |
| `.gitignore` | Exclut l'état Terraform, le dossier `.terraform` et les fichiers `.tfvars` |
| `Makefile` | Raccourcis pour les commandes Terraform |
| `.github/workflows/terraform.yml` | Contrôles de format et de validation sur GitHub Actions |

## Installation

Outils nécessaires :

- Terraform (version 1.5.0 ou plus)
- Docker Desktop ou Docker Engine
- Git

Vérifier les installations :

```bash
terraform -version
docker --version
docker info
git --version
```

Docker doit être démarré, sinon `docker info` renvoie une erreur.

Récupérer le projet :

```bash
git clone https://github.com/Oussamaaor/tp-iac.git
cd tp-iac
```

## Déploiement

```bash
terraform init
terraform fmt -recursive
terraform validate
terraform plan
terraform apply
```

`terraform apply` affiche le plan et demande une confirmation : répondre `yes`.

Avec le Makefile :

```bash
make init
make validate
make plan
make apply
```

### Environnement de développement

Le fichier `dev.tfvars` n'est pas dans le dépôt (les fichiers `.tfvars` sont ignorés). Pour le recréer :

```hcl
container_name = "tp-iac-dev"
external_port  = 8081
```

Puis :

```bash
terraform plan -var-file="dev.tfvars"
terraform apply -var-file="dev.tfvars"
```

Le site est alors sur http://localhost:8081. Attention : c'est le même état Terraform, donc le conteneur et le réseau par défaut sont remplacés.

## Tests

Afficher les informations du déploiement :

```bash
terraform output
```

Ouvrir http://localhost:8080 dans un navigateur, ou :

```bash
curl http://localhost:8080
docker ps
docker network ls
docker network inspect tp-iac-nginx-network
```

Résultats obtenus :

- la page « Infrastructure déployée avec Terraform » s'affiche ;
- `docker ps` montre le conteneur `tp-iac-nginx` avec le port `127.0.0.1:8080->80/tcp` ;
- le réseau `tp-iac-nginx-network` existe et contient le conteneur.

Une modification de `html/index.html` est visible directement en rechargeant la page, sans redéployer.

## Destruction

Voir d'abord ce qui va être supprimé :

```bash
terraform plan -destroy
```

Puis supprimer :

```bash
terraform destroy
```

Vérifier :

```bash
docker ps -a
docker network ls
terraform state list
```

Le conteneur et le réseau ont disparu et `terraform state list` ne renvoie plus rien. L'image `nginx:alpine` reste sur la machine car `keep_locally = true`.
