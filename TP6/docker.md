# TP-6

## Configuration

On va créer deux conteneurs master et slave, les deux pourront communiquer ensemble sauf que l'un pourra écrire et lire, tandis que l'autre pourra seulement lire.

### Master

Pour Master on lui fait une conf classique.

````conf
# /etc/mysql/mariadb.conf.d/master.cnf

[mariadb]
log-bin
server_id=1
log-basename=master
binlog-format=mixed
````

Ensuite, on va créer un utilisateur autorisé à faire de la réplication.

````sql
CREATE USER IF NOT EXISTS 'replicant'@'%' identified by 'password';

grant replication slave on *.* to replicant;

flush privileges;
````

### Slave
Ensuite il faut configurer le slave.

````conf
# /etc/mysql/mariadb.conf.d/slave.cnf

[mariadb]
server_id=2
````

Ensuite, on va ajouter l'état "Slave" en se connectant au "Master"

````sql
CHANGE MASTER TO
MASTER_HOST='master-replication.localhost',
MASTER_USER='replicant',
MASTER_PASSWORD='password',
MASTER_PORT=3306,
MASTER_LOG_FILE='master1-bin.000003',
MASTER_LOG_POS=344,
MASTER_CONNECT_RETRY=10;

START SLAVE;
````

Normalement si on fait les tests tout devrait fonctionner, avec un SHOW SLAVE STATUS pour voir le status du slave.
