# TP IaC - Terraform et Docker

Ce projet déploie un serveur Nginx dans un conteneur Docker avec Terraform.

## Installation

Il faut installer Terraform, Docker Desktop et Git.

Pour vérifier :

```bash
terraform -version
docker --version
docker info
git --version
```

Docker Desktop doit être lancé avant de continuer.

## Déploiement

```bash
terraform init
terraform validate
terraform plan
terraform apply
```

Taper `yes` quand Terraform demande la confirmation.

On peut aussi utiliser le Makefile : `make init`, `make validate`, `make plan`, `make apply`.

Pour l'environnement dev, créer un fichier `dev.tfvars` (il n'est pas dans le dépôt à cause du .gitignore) :

```hcl
container_name = "tp-iac-dev"
external_port  = 8081
```

puis lancer `terraform apply -var-file="dev.tfvars"`.

## Tests

```bash
terraform output
curl http://localhost:8080
docker ps
docker network ls
```

On peut aussi ouvrir http://localhost:8080 dans le navigateur, la page du TP s'affiche.

## Destruction

```bash
terraform plan -destroy
terraform destroy
```

Ensuite on vérifie avec `docker ps -a`, `docker network ls` et `terraform state list` que le conteneur et le réseau sont supprimés. L'image nginx reste sur la machine car `keep_locally = true`.
