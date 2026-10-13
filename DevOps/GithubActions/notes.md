My first CI/CD project

Github Actions

The cloud deployment resources

AWS Cloud Deployment Architecture for the University Research Portal Security Application.
![Cloud-architecture](screenshots/Cloud-architecture.gif)

## Code

github

- **[University Research Portal — Docker Deployment](https://github.com/pritamdhurel-tech/University_Research_Portal_Security_Application)**

## Dockerfiles

# backend

![backendDockerfile](screenshots/backendDockerfile.png)

# frontend

![frontendDockerfile](screenshots/frontendDockerfile.png)

# Docker compose file

![dockerCompose](screenshots/dockerCompose.png)

# github actions

- docker.yaml
  ![deploy.yaml](screenshots/deploy.yaml.png)

# test files

![app.test.js](screenshots/app.test.js.png)

# Cloud - AWS

1. VPC - (name: test-1)
   ![resourceMap](screenshots/resourceMap.png)

2. Subnet
   - Public Ip (10.0.0.0/24 CIDR range)
     ![resourceMap](screenshots/resourceMap.png)

3. Internet Gateway (name: test-internet-gateway)
   ![resourceMap](screenshots/resourceMap.png)

4. Route table
   routes
   - 0.0.0.0 --> Internet Gateway
   - 10.0.0.0 --> local
     ![resourceMap](screenshots/resourceMap.png)

5. Security Groups
   - SSH
   - HTTP
     ![securityGroupImg](screenshots/securityGroupImg.png)

6. Resource Map
   ![resourceMap](screenshots/resourceMap.png)

7. Instance
   ![instanceImage](screenshots/InstanceImage.png)
