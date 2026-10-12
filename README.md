# TP IaC - Terraform et Docker

Le but du TP c'est de lancer un serveur Nginx dans Docker mais avec Terraform, sans rien faire à la main.

## Installation

Il faut Terraform, Docker Desktop et Git. Pour vérifier que c'est bien installé :

```bash
terraform -version
docker --version
docker info
git --version
```

Attention il faut que Docker Desktop soit lancé sinon ça marche pas.

Pour récupérer le projet :

```bash
git clone https://github.com/Oussamaaor/tp-iac.git
cd tp-iac
```

## Déploiement

```bash
terraform init
terraform validate
terraform plan
terraform apply
```

Il faut taper `yes` à la fin du apply.

Sinon avec le Makefile : `make init`, `make validate`, `make plan`, `make apply`.

Par défaut le conteneur c'est `tp-iac-web`, le réseau `tp-iac-network` et le port 8082.

Pour tester en dev il faut créer un fichier `dev.tfvars` (il est pas sur GitHub à cause du .gitignore) :

```hcl
container_name = "tp-iac-dev"
external_port  = 8081
```

et lancer `terraform apply -var-file="dev.tfvars"`.

## Tests

```bash
terraform output
curl http://localhost:8082
docker ps
docker network ls
```

Ou juste ouvrir http://localhost:8082 dans le navigateur.

## Destruction

```bash
terraform plan -destroy
terraform destroy
```

Après on vérifie avec `docker ps -a`, `docker network ls` et `terraform state list` qu'il reste plus rien. L'image nginx reste quand même sur le PC à cause de `keep_locally = true`.

## Questions de compréhension

1. init prépare le projet et télécharge le provider, plan montre ce qui va changer sans rien toucher, apply fait vraiment les changements
2. le provider c'est ce qui fait le lien entre Terraform et l'outil, ici Docker
3. le tfstate garde ce que Terraform a créé pour comparer avec le code
4. les variables pour changer des valeurs sans toucher au code, les outputs pour afficher des infos à la fin (l'url par ex)
5. une dérive c'est quand ce qui tourne vraiment est plus pareil que le code (ex : conteneur arrêté à la main)
6. à la main c'est long et on peut se tromper, avec l'IaC tout est dans des fichiers donc on peut refaire pareil autant de fois qu'on veut
7. garder l'historique, bosser à plusieurs, revenir en arrière
8. la CI vérifie le code (format, validate), le CD déploie vraiment
9. pour voir avant ce qui va être supprimé ou remplacé et éviter les mauvaises surprises
10. pas mettre le tfstate ni les .tfvars sur GitHub, pas mettre de mots de passe dans le code, et pas supprimer le state à la main
