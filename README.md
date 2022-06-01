# migrate_wait

Just a simple image with go-migrate and waitforit installed, which allows database migrations to be done AFTER waiting for a database to become available.

Useful in a docker compose context where a new database is required with an empty schema, but a mechanism is needed to wait for the database to be created first. There is no other reliable way to wait for this in docker compose.

Docker compose example usage:

```
version: '3'

services: 
  ...
 
  migrate-example-db:
    image: migrate_wait:latest
    command: -c "waitforit -timeout=30 -retry=1000 -debug -address=tcp://db:3306 -- echo starting migration && migrate -path=/migrations -database mysql://root:Pass1word@tcp\(db:3306\)/example up"
    volumes:
      - migrations/_db:/migrations
```
