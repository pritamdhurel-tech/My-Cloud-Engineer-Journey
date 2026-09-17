# Weekly Log

Dated entries on what I worked on, what broke, and what I learned from it.

## Week of Sept 15, 2026

- Set up Ubuntu Server 26.04 LTS in VMware Workstation as a dedicated Linux/Docker practice environment
- Installed Docker via the official repository, worked through image/container/Compose fundamentals
- Containerized the [University Research Portal](https://github.com/pritamdhurel-tech/University_Research_Portal) (PERN stack) with Docker Compose — nginx reverse proxy for the frontend, automated Prisma migrations on backend startup
- Debugged a database startup race condition (backend crash-looping until a Postgres healthcheck was added) and a CORS error that turned out to be a masked migration failure (`prisma migrate deploy` had never run against the fresh container) — full writeup in the project's [DEPLOYMENT.md](https://github.com/pritamdhurel-tech/University_Research_Portal_Security_Application/blob/main/deployment/docker_deployment.md)
