# - Tuto Docker et AWS

<h2>Docker en local avec un docker-compose</h2>

1) Installez Docker , si vous ne l'avez pas déjà, ajoutez un Dockerfile à la racine du projet :

2) Ensuite, ajoutez un fichier docker-compose.yml à la racine du projet :

3) Construisez l'image avec le docker compose: docker-compose build

4) Une fois l'image créée, exécutez le conteneur : docker-compose up -d

5) Accédez à http://localhost:8008 pour afficher la requete de votre fastapi

# -------------------------------------------- #

<h3>Objectif</h3>

L'objectif de ce guide étape par étape est de vous aider à déployer
facilement votre conteneur Docker sur Amazon EC2 à l'aide d'Amazon ECS et ECR.

<h3>Docker</h3>

Docker est un logiciel libre permettant de lancer des applications dans des conteneurs logiciels.

<h3>EC2</h3>

Amazon Elastic Compute Cloud est un service proposé par Amazon permettant à des
tiers de louer des serveurs sur lesquels exécuter leurs propres applications web

<h3>ECR</h3>

Amazon Elastic Container Registry est un registre de conteneurs entièrement géré
qui facilite le stockage, la gestion, le partage et le déploiement de vos images


![shema-aws](/image/AWS-SCHEMA.png)


# -------------------------------------------- #

<h2>Docker en prod</h2>

### Assurez-vous d'avoir un compte AWS

https://console.aws.amazon.com/console/home?region=us-east-1

### téléchargez Docker Desktop

--> https://www.docker.com/products/docker-desktop

### inscrivez-vous pour un Docker ID

--> https://hub.docker.com/editions/community/docker-ce-desktop-mac?utm_source=awsedge

##### Votre identifiant Docker gratuit vous donne accès aux référentiels Docker Hub 
##### Vous pouvez également vous connecter via la CLI à l'aide de la docker login commande

### ----------------------------- ###


### Créé une clefs d'identifiant utilisateur grace a ce lien

https://console.aws.amazon.com/iam/home#/security_credentials

![securité](/image/security.png)

--> procédure pour le déploiement via ECS

### Installer AWS Cli

L'AWS Command Line Interface (AWS CLI) est un outil à code source libre qui vous permet d'interagir avec les services AWS à l'aide des commandes du terminal de ligne de commande.

``` shell
$ brew install awscli 
$ aws configure 
AWS Access Key ID [Aucun] : <YOUR_ACCESS_KEY_HERE> 
AWS Secret Access Key [Aucun] : <YOUR_SECRET_KEY_HERE> 
Nom de la région par défaut [Aucun] : us-west-2 
Format de sortie par défaut [Aucun] : json
```


## ------------------------------------------------------- ##

#### 1) installé et configuré l'AWS CLI

--> https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-mac.html#cliv2-mac-install-gui

#### 2) Votre utilisateur doit disposer des autorisations IAM requises pour accéder au service Amazon ECR

IAM permet de contrôler les personnes qui s'authentifient (connectées) et sont autorisées (disposent d'autorisation) à utiliser les ressources.

https://console.aws.amazon.com/iam/home#/users$new?step=details

1) mettre un nom d utilisateur
2) acces a la programation
3) mettre les droits administrateurs
4) créé l'user et enregistres les secret key



# ----------------------------------- #


# Facon de faire numéro 1

## Service de cluster Amazon Elastic

### 1) Allez ici pour créer un cluster

https://us-east-1.console.aws.amazon.com/ecs/home?region=us-east-1#/clusters

### 2) Cliquez sur Créer un cluster

Cela créra également une instance EC2 pour que vous puissiez exécuter votre image Docker.

![cluster](/image/cluster.png))

### 3) Parametrage du cluster

![cluster2](/image/cluster2.png))

Choisir le cluster 
Mettre un nom de cluster
Mettre une instance t2.micro
1 instance
ensuite tout laissé par défault

### 4) Afficher le cluster et attendre 1 ou 2 minutes en affichant l'instance

![cluster3](/image/cluster3.png))

### 5) Créé un référentiel privé sur Elastic Container Register, qui vous permet de déployer votre conteneur Docker sur AWS

https://us-east-1.console.aws.amazon.com/ecr/repositories?region=us-east-1

![repo](/image/repo.png))

### 6)  Cliquez sur Afficher les commandes push et suivez les instructions pour déployer votre application

### 7) etre sur d'avoir un dockerfile  bien configuré dans son projet

commande 1 : authentifiez votre client Docker auprès de votre registre : login succeed apparaitra
commande 2 : On créé l'image docker
commande 3 : Une fois que la création est terminée, balisez votre image pour pouvoir la transférer dans ce référentiel
commande 4 : Exécutez la commande 4 pour transférer cette image dans le dernier référentiel AWS que vous avez créé (push)

### 8) Actualisez. Vous verrez que desormais votre image apparait dans votre référentiel

Copier votre URI et passez a la suite

![repo2](/image/repo2.png))

![repo3](/image/repo3.png))


### 7) sur la même page du référentiel allez dans task definition

1) choisir EC2
2) donner un nom par exemple : task-demo-simplonien
3) mettre la memoire a 512
4) mettre le processeur a 512
5) appuyer sur ajouter le conteneur

1) mettre un nom par exemple : onteneur-demo-simplonien
2) dans image collé l'uri copié juste avant
3) dans Mappages de ports ajouter hote 80 et port 80
4) ajouter un autre mappage hote 8080 et port 8080
5) tout laissé par défault le reste et ajouter
6) et enfin appuyer sur créer

### une fois créé allez sur créer un service

![service](/image/service.png))

1) choisir EC2
2) un nom comme par exemple server-simplonien
3) nombre de tâche : 1
4) le reste sur default t appuyer sur etape suivante 
5) encore tout a default et etape suivante
6) encore tout a default et etape suivante
7) appuyer sur créer un service
8) afficher le service
9) actualiser au bout de 1 a 2 minutes jusqu'a ce que le statut soit RUNNING

### acceder a votre app

1) aller sur votre instance
https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:instanceState=running
2) allez dans mise en reseau
3) copier l' Adresse IPv4 publique dans le navigateur

BRAVO

# ---------------------------------------------------- #

# Facon de faire numéro 2 

#### 1) Lancez une instance avec l' AMI Amazon Linux 2

--> https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:

#### 2) Connectez-vous à votre instance en suivant la commande example client ssh

--> vous etes desormais connecte a l'instance Linux

#### 3) Mettez à jour les packages installés

````shell
sudo yum update -y
````

#### 4) Installez le package Docker Engine le plus récent. Amazon Linux 2

````shell
sudo amazon-linux-extras install docker
````

#### 5) Lancez le service Docker

````shell
sudo service docker start
````

#### 6) Ajoutez ec2-user au groupe docker afin de pouvoir exécuter les commandes Docker sans utiliser sudo

````shell
sudo usermod -a -G docker ec2-user
````

#### 7) Déconnectez-vous et reconnectez-vous pour récupérer les nouvelles autorisations de groupe docker

````shell
pomme q
````
````shell
sudo usermod -a -G docker ec2-user
````

````shell
ssh -i "clefs_de_la_demo_pour_micka.pem" ec2-user@ec2-3-237-68-44.compute-1.amazonaws.com
````

#### 8) Vérifiez que ec2-user peut exécuter les commandes Docker sans sudo

````shell
docker info
````

<h3>Transmettre votre image à Amazon Elastic Container Registry ECR</h3>

--> https://console.aws.amazon.com/ecr/repositories?region=us-east-1


#### 3) Créez un référentiel Amazon ECR pour stocker votre image

--> récupérer la valeur dans le dictionnaire qui apparait :

"repositoryUri": "426584999926.dkr.ecr.us-east-1.amazonaws.com/repo_demo_mickael"

#### 4) Balisez l'image avec la valeur repositoryUri de l'étape précédente

##### 5) Exécutez la commande aws ecr get-login-password.

--> Spécifiez l'URI du registre sur lequel vous souhaitez vous authentifier

````shell
aws ecr get-login-password | docker login --username AWS --password-stdin aws_account_id.dkr.ecr.region.amazonaws.com
````

#### EXEMPLE

````shell
aws ecr get-login-password | docker login --username AWS --password-stdin 426584999926.dkr.ecr.us-east-1.amazonaws.com/repo_demo_mickael
````
Si ca a marché il y aura marqué "Login Succeeded"

#### 6) Créez votre image Docker à l'aide de la commande suivante

````shell
docker build -t 426584999926.dkr.ecr.us-east-1.amazonaws.com/repo_demo_mickael .
````
#### 7) pousser l'image sur le repo aws

````shell
docker push 426584999926.dkr.ecr.us-east-1.amazonaws.com/repo_demo_mickael:latest
````
--> si ca marche l'image apparaitra dans le repo créé sur amazon

## Créé ensuite votre cluster pour héberger votre container ( voir facon de faire numéro 2  )























