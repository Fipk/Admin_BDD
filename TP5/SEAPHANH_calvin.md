# TP - 5 - Rendu 

## 1 - Installation : 


Créer le docker compose avec les paramètres suivant : 

Je tiens à noter qu'au départ je me suis tromper et j'ai créé mon fichier docker compose avec le nom dockerfile-compose.yml. J'ai donc du précisé
le nom du fichier à chaque fois.
```
version:  '3.7'

services:
        mysql:
          image: mysql:5.7
          restart: on-failure
          environment:
              MYSQL_ROOT_PASSWORD: password
          volumes:
          - ./mysql:/var/lib/mysql
          - ./backups:/backups

        maria:
          image: mariadb:10.4
          restart: on-failure
          environment:
            MYSQL_ROOT_PASSWORD: password
          volumes:
          - ./maria:/var/lib/mysql
          - ./backups:/backups  
```
On va ensuite lancer les deux dockers mysql et maria

```
docker-compose -f dockerfile-compose.yml up -d
```

## 2 - Mysql :

### 2.1 - La connexion : 

Pour se connecter au mysql il faudra taper la commande:
```
docker-compose -f dockerfile-compose.yml exec mysql bash
```

### 2.2 - Le Dump : 

Il faut ensuite une base de données pour tester.

Tout d'abord il faut se connecter au mysql du docker mysql :

```
mysql -uroot -ppassword
```
Ensuite créer la bbd pour tester :
```
CREATE DATABASE test;

USE test;

CREATE TABLE public_test (event_test_date DATE,event_test_name VARCHAR(255),event_test_age TINYINT(127));

INSERT INTO  public_test(event_test_date, event_test_name, event_test_age) VALUES('2015-01-05','Yes','1');
INSERT INTO  public_test(event_test_date, event_test_name, event_test_age) VALUES('2009-07-02','No','5');

```

Maintenant nous allons créer le dump : 

```
mysqldump -uroot -ppassword events > backups/dump.sql
```

## 3 - MariaDB 

### 3.1 - Paramétrage :
Connecter vous au docker Mariadb : 

```
docker-compose -f dockerfile-compose.yml exec maria bash
```
Premiérement il faut vérifier si le backup c'est bien fait en allant dans /backups.
Ensuite il va falloir créer la même base de données en se reconnectant à mysql, il faut que la bdd est le même nom que sur le docker mysql.

```
mysql -uroot -ppassword
```
```
CREATE DATABASE events;
```

### 3.2 - Importation : 

Une fois votre bdd est créé entrer la commande pour importé la bdd : 

```
mysql -uroot -ppassword  events < backups/dump.sql
```
La base de donnée est maintenant importé : 

```
MariaDB [events]> select * from public_test;
+------------+--------------+-----------------------+
| event_test_date | event_test_name  | event_test_age |
+------------+--------------+-----------------------+
| 2015-01-05 | Yes          |                     1 |
| 2009-07-02 | No           |                     5 |
+------------+--------------+-----------------------+
```
