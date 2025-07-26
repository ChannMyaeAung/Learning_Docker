# Docker Notes

- **Containerization platform** that packages an application and its dependencies into a lightweight, portable unit called a **container**.
- Containers share the host OS kernel but run in isolated user spaces, making them more efficient than full VMs.
- **Image**: A read‑only template (like a snapshot) defining what goes into a container (OS layers, runtime, app code).
- **Container**: A running instance of an image, with its own isolated filesystem, network interface, and process space.
- **Registry**: A storage system (e.g., Docker Hub) where images are pushed/pulled.

```bash
docker run hello-world
```



## Merging Containers & Images

**List running containers**

```bash
docker ps
```

**List all containers (including stopped)**

```bash
docker ps -a
```

**Stop a container**

```bash
docker stop <container_id_or_name>
```

**Remove a container**

```bash
docker rm <container_id_or_name>
```

**List images**

```bash
docker images
```

**Remove an image**

```bash
docker rmi <image_id_or_name>
```

---

## Writing a Dockerfile

- A **Dockerfile** automates image creation.

```dockerfile
# 1. Base image
FROM node:20-alpine

# 2. Create app directory
WORKDIR /usr/src/app

# 3. Copy and install dependencies
COPY package*.json ./
RUN npm install --production

# 4. Copy app source
COPY . .

# 5. Expose port and define default command
EXPOSE 3000
CMD ["node", "index.js"]
```

---

## Volumes: Persisting Data

- Now our database files live in `db_data` on the host, surviving container restarts/removals.

```bash
# Create and mount a named volume
docker volume create db_data
docker run -d \
  -v db_data:/var/lib/mysql \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=secret \
  mysql:8
```

---

## Networking: Connecting Containers

- By default containers share the `bridge` network. 
- We can create our own:

```bash
docker network create backend-net

docker run -d \
  --network backend-net \
  --name api-server \
  my-api-image

docker run -d \
  --network backend-net \
  --name db-server \
  -e MYSQL_ROOT_PASSWORD=secret \
  mysql:8
```

- Containers on `backend-net` can reach each other by container name (e.g., `api-server` can connect to host `db-server:3306`).

---

## Docker Compose (Multi-Container Apps)

- Define services in `docker-compose.yml`

```yaml
version: "3.8"
services:
  web:
    build: ./web
    ports:
      - "8080:80"
    depends_on:
      - api

  api:
    build: ./api
    environment:
      - DATABASE_URL=mysql://user:pass@db:3306/app_db
    depends_on:
      - db

  db:
    image: mysql:8
    environment:
      MYSQL_USER: user
      MYSQL_PASSWORD: pass
      MYSQL_DATABASE: app_db
      MYSQL_ROOT_PASSWORD: rootpass
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:

```

We can start everything with

```bash
docker-compose up -d
```

