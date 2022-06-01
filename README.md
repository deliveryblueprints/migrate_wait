# migrate_wait

Just a simple image with go-migrate and waitforit installed, which allows database migrations to be done AFTER waiting for a database to become available.

Useful in a docker compose context where a new database is required with an empty schema, but a mechanism is needed to wait for the database to be created first. There is no other reliable way to wait for this in docker compose.

Docker compose example usage:

```
version: '3'

networks:
  migrate-test:
    driver: bridge

services:  
  db:
    image: mariadb:10.5
    ports:
      - "3308:3306"
    expose:
      - "3306"
    environment:
      MYSQL_ROOT_PASSWORD: Pass1word
    restart: on-failure
    volumes:
      - ./init:/docker-entrypoint-initdb.d
    networks:
      - migrate-test
  migrate-rules-db:
    image: migrate_wait:latest
    command: -c "waitforit -timeout=30 -retry=1000 -debug -address=tcp://db:3306 -- echo starting migration && migrate -path=/migrations -database mysql://root:Pass1word@tcp\(db:3306\)/rules up"
    networks:
      - migrate-test
```
