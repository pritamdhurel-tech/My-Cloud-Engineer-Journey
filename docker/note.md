# Docker Notes

Docker packages an application and its dependencies into an isolated, portable container. The workflow is: **write a Dockerfile → build an image → run a container.**

## Installing Docker (Ubuntu Server, official repository)

```bash
# 1. Update packages
sudo apt update && sudo apt upgrade -y

# 2. Install prerequisites
sudo apt install -y ca-certificates curl

# 3. Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# 4. Add the Docker repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5. Install Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 6. Verify
sudo docker run hello-world   # should print "Hello from Docker"
```

## Core commands

| Command                            | Purpose                                                     |
| ---------------------------------- | ----------------------------------------------------------- |
| `docker build -t <name> .`         | Build an image from a Dockerfile in the current directory   |
| `docker run <image>`               | Run a container from an image                               |
| `docker ps` / `docker ps -a`       | List running containers / all containers including stopped  |
| `docker exec -it <container> bash` | Open an interactive shell inside a running container        |
| `docker stop <container>`          | Stop a container                                            |
| `docker rm <container>`            | Delete a stopped container                                  |
| `docker logs <container>`          | View a container's logs                                     |
| `docker images`                    | List available images                                       |
| `docker rmi <image>`               | Remove an image                                             |
| `docker network ls`                | List Docker networks                                        |
| `docker compose up`                | Start a multi-container app defined in `docker-compose.yml` |
| `docker compose down`              | Stop and remove that environment                            |
| `docker compose logs`              | View logs across all services in a Compose project          |

## Practice build: Node + Postgres

**1. Project setup**

```bash
mkdir myfirstapp && cd myfirstapp
nano Dockerfile
```

```dockerfile
FROM node:20
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

Minimal test app:

```javascript
// app.js
const http = require("http");
http.createServer((req, res) => res.end("Hello from Docker")).listen(3000);
```

```json
// package.json
{
  "name": "myfirstapp",
  "version": "1.0.0",
  "main": "app.js"
}
```

**2. Build and run**

```bash
docker build -t myfirstapp .
docker images   # confirm it exists
docker run -d -p 3000:3000 --name myfirstapp-container myfirstapp
```

- `-d` — detached, runs in the background
- `-p 3000:3000` — maps host port to container port
- `--name` — gives the container a readable name

**3. Verify**

```bash
docker ps
curl localhost:3000   # should return "Hello from Docker"
```

**4. Compose — running multiple services together**

Instead of starting each container manually, `docker-compose.yml` defines the whole stack in one file:

```yaml
services:
  app:
    build: .
    ports:
      - "3000:3000"
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: examplepass
    ports:
      - "5432:5432"
```

```bash
docker stop myfirstapp-container   # stop the manually-run container first (port conflict otherwise)
docker compose up -d
docker compose ps
```

**Key concept:** inside Compose's network, the app connects to the database using the _service name_ (`db`), not `localhost` — each service gets its own hostname on Docker's internal network.

## Multi-stage builds (for a frontend, e.g. React/Vite)

Used when the build tooling (Node) isn't needed in the final running container:

```dockerfile
FROM node:20 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
```

Stage one builds the static files; stage two only keeps the compiled output, served by a lightweight nginx image — the Node toolchain is discarded from the final image.

## Real issues hit in practice

See [University Research Portal — DEPLOYMENT.md](https://github.com/pritamdhurel-tech/University_Research_Portal/blob/main/DEPLOYMENT.md) for a full log of real problems solved while deploying an actual app with Docker Compose: a database startup race condition, a CORS error that turned out to be a masked migration failure, and automating Prisma migrations on container startup.
