## docker-compose example

As already stated this project is an implementation of such board and made of 3 separate Docker containers that holds:

- MYSQL database
- Python Backend

We use nginx as reverse proxy for port mapping from 500 to 80: **http://localhost/**

### Prerequisites

In order to run this application you need to install two tools: **Docker** & **Docker Compose**.

Instructions how to install **Docker** on [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/), [Windows](https://docs.docker.com/docker-for-windows/install/), [Mac](https://docs.docker.com/docker-for-mac/install/).

**Docker Compose** is already included in installation packs for *Windows* and *Mac*, so only Ubuntu users needs to follow [this instructions](https://docs.docker.com/compose/install/).


### How to run it?

The entire application can be run with a single command on a terminal:

```
$ docker-compose up -d
```

If you want to stop it, use the following command:

```
$ docker-compose down
```

---

#### mysql (Database)

After running the app it can be accessible using these connectors:

#### Docker Volumes
Every time we take down our containers, we remove them and therefore lose the data we stored in previous sessions. To avoid that and persist DB data between different containers, we can exploit named volumes. For this, we simply define a named volume in the Compose file and specify a mount point for it in the db service as shown below:

#### Docker Secrets
As we can observe in the Compose file, we set the db password in plain text. To avoid this, we can exploit docker secrets to have the password stored and share it securely with the services that need it. We can define secrets and reference them in services as below. The password is being stored locally in the project/db/password.txt file and mounted in the containers under /run/secrets/<secret-name>.

Like other parts of application Postgres database is containerized and
the definition of its Docker container can be found in
*docker-compose.yml* file.

```yml
db:
    image: mysql:8.0.19
    command: '--default-authentication-plugin=mysql_native_password'
    restart: always
    secrets:
      - db-password
    volumes:
      - db-data:/var/lib/mysql
    networks:
      - backend-network
    environment:
      - MYSQL_DATABASE=example
      - MYSQL_ROOT_PASSWORD_FILE=/run/secrets/db-password
```

#### python-app (REST API)

This is a python based application that connects with a
database that and expose the REST endpoints that can be consumed by
frontend. It supports multiple HTTP REST methods like GET, POST, PUT and
DELETE for one resource

This app is also put in Docker container and its definition can be found
in a file *app/Dockerfile*. 
