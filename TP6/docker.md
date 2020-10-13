version "3.7"

services:
    maria1:
        image: mariadb:10.4
        ports:
            - 3306
        environment:
            - MYSQL_ROOT_PASSWORD: password
        networks:
            - mon-reseau-3
    maria2:
        image: mariadb:10.4
        environment:
            - MYSQL_ROOT_PASSWORD: password
        networks:
            - mon-reseau-3

networks:
    mon-reseau-3: