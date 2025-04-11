# Guide Simplifié pour Débutants : Docker avec Symfony

## Préparation : Prérequis
Avant de commencer, installe ces outils :
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- [Git](https://git-scm.com/downloads)

## 1. Installation du projet
### Cloner le dépôt Git
```bash
git clone https://github.com/votre-utilisateur/test_docker_symfony.git
cd test_docker_symfony
```

### Construire les conteneurs Docker
```bash
docker compose build
```

### Construire les conteneurs Docker sans cache
```bash
docker compose build --no-cache
```

### Démarrer les services Docker
```bash
docker compose up -d
```

### Créer un nouveau projet Symfony (si nécessaire)
```bash
##docker exec -w /var/www (container name) symfony new --webapp app

#dans le powershell
$env:PROJECT_DIR = "home_controller"

#ensuite on peut l'utiliser pour créer symfony en utilisant le nom du container unique
docker exec -w /var/www $env:PROJECT_DIR symfony new --webapp app

```
## 2. Structure du projet
```
test_docker_symfony/
├── docker-compose.yml
├── php/
│   ├── Dockerfile
│   └── vhosts/vhosts.conf
└── project/
    ├── public/ (fichiers web accessibles)
    ├── src/ (code source PHP)
    └── config, bin, templates, var, vendor...
```

## 3. Commandes essentielles Docker
- **Démarrer les services**
```bash
docker compose up -d
```
- **Arrêter les services**
```bash
docker compose down
```
- **Accéder au conteneur**
```bash
# voir la création de symfony
docker exec -it $env:PROJECT_DIR bash
```

### Régler les permissions (optionnel)
Pour éviter les problèmes de permissions :
```bash
chown -R www-data:www-data /var/www/app/var/cache
```

- **Voir les logs**
```bash
docker compose logs
```

## 4. Accéder aux services

- **Application Symfony** : [http://localhost:8741](http://localhost:8741)

- **phpMyAdmin** : [http://localhost:8080](http://localhost:8080)
  - Serveur : `db`
  - Utilisateur : `root`
  - Mot de passe : vide

  Pour accéder à phpMyAdmin, entre simplement l'URL dans ton navigateur puis connecte-toi avec les informations ci-dessus.

- **MailDev** : [http://localhost:8081](http://localhost:8081)

## 5. Gestion des dépendances avec Composer
- **Installer les dépendances**
```bash
docker exec -w /var/www/project $env:PROJECT_DIR composer install
```
- **Ajouter une nouvelle dépendance (exemple twig)**
```bash
docker exec -w /var/www/project $env:PROJECT_DIR composer require twig
```

### Monter en version Symfony
Pour passer à une version plus récente de Symfony :
```bash
docker exec -w /var/www/project $env:PROJECT_DIR composer update symfony/*
```

## 6. Débogage
- **Activer la barre de débogage Symfony**
```bash
docker exec -w /var/www/project $env:PROJECT_DIR composer require symfony/web-profiler-bundle --dev
docker exec -w /var/www/project $env:PROJECT_DIR bin/console cache:clear
```

- **Voir les logs Apache**
```bash
docker exec -it   cat /var/log/apache2/error.log
```

- **Activer le mode debug Symfony**
```bash
docker exec -w /var/www/project $env:PROJECT_DIR bin/console debug:config framework
```

## 7. Problèmes fréquents
### Erreur Doctrine (base de données)
Vérifier extensions PHP (`pdo_mysql` activée) :
```bash
docker exec -it $env:PROJECT_DIR docker-php-ext-install pdo_mysql
```
Créer la base de données :
```bash
docker exec -w /var/www/project $env:PROJECT_DIR bin/console doctrine:database:create
```

- **Erreur 404 (Page Not Found)** : Vérifie l'installation Symfony et la configuration Apache (`vhosts.conf`).

- **Problèmes de cache Symfony** : Vider le cache
```bash
docker exec -w /var/www/project $env:PROJECT_DIR bin/console cache:clear
```

- **Problèmes de permissions** :
```bash
sudo chown -R $USER:$USER project
```

Remarque : Utilisation du shell interactif Docker
Lorsque tu accèdes au conteneur Docker via la commande :

bash
Copier
Modifier
docker exec -it $env:PROJECT_DIR bash
par défaut, tu es situé dans le répertoire /var/www. À cet endroit, le fichier bin/console n'existe pas car ton projet Symfony se trouve dans le sous-dossier /var/www/project.

Tu dois donc te déplacer vers le dossier du projet Symfony pour exécuter les commandes Symfony :

bash
Copier
Modifier
cd project
php bin/console make:controller chatController
Cette étape est essentielle pour que Symfony reconnaisse tes commandes et les exécute correctement.

Ce guide permet de démarrer rapidement un environnement de développement Symfony avec Docker.

