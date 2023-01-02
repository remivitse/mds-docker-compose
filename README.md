# Docker Compose

On crée 4 containers :
* mds_httpd à partir d'un Dockerfile
* mds_mysql depuis l'image mysql
* mds_php depuis l'image php
* mds_phpmyadmin depuis l'image phpmyadmin

## mds_httpd
Dans `httpd/` se trouvent :
* un `Dockerfile`
* un fichier de configuration apache `local.apache.conf`

Le dockerfile build à partir de l'image httpd, mais il copie le fichier de configuration et ajoute une ligne à la fin du `httpd.conf` par défaut pour inclure cette configuration supplémentaire.

Ce fichier de configuration permet d'accéder correctement au port 9000 du container (par proxy), afin de pouvoir executer les fichiers PHP. Autrement, le serveur n'interprète pas le code et se contente de l'afficher.

Le répertoire `html/` contient les pages à afficher. Un volume lie son contenu au dossier `/var/www/html/`.
> `index.php` affiche `phpinfo()`.

## mds_mysql
> L'instruction `platform: linux/x86_64` permet d'éviter les problèmes de compatibilité avec l'architecture arm64.

On lie le port 3306 du container avec le port 3306 de l'hôte pour permettre l'accès à la base de données sur ce port une fois le container démarré.

Un volume est crée afin de ne pas perdre les données à chaque fin de cycle de vie du container.

On définit les variables d'environnement nécessaires au fonctionnement de MySQL.

## mds_php
Un volume est crée pour que le code sur l'hôte sous `html/` puisse être exécuté depuis le container.

Le port 80 est exposé puisqu'il est nécessaire à l'execution du code PHP (proxy 80 -> 9000).

> Sans Apache, PHP ne nous sert à rien, le service `php` dépend donc de `httpd`.

## mds_phpmyadmin
PhpMyAdmin est accessible sur le port 8080 de l'hôte, puisque le port 8000 est occupé par Apache.

Comme pour MySQL, on définit les variables d'environnement nécessaires au fonctionnement du service.

> PhpMyAdmin est une interface graphique pour MySQL, le service `phpmyadmin` dépend donc de `mysql`.
