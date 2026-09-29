# here some of the social media devops questions

# 1. how do you speed up a slow databases ?

# ans. find slow queries, optimized indexes, add caching & use read replicas.

# 2. how do youo design a highly available application ?

# ans. Run multiple instances across AZs behind a load balancer, with automatic failover.

# 3. how do you handel a sudden 10x traffic spike ?

# ans. use Autoscaling, caching, load balancing & scale the instance according.

# 4. How do you acheive zero-downtime deployment ?

# ans. using Rolling, Blue-Green deployment, canary deployments with health checks & rollback.

# 5. How do you troubleshoot an intermittently failing service ?

# ans. check logs, metrics & traces to the identify wether the issue is in the service or a dependency.

# 6. how do you design a scalable CI/CD pipeline?

# ans. parallelize jobs, cache dependency, store artifacts & automate deployment.

# 7. How do you secure a kubernetes cluster.

# ans. use least privilege Rbac, network policies seure secrets & image scanning levy erno falco & many ore things.

# 8. how do you design for disaster recovery?

# ans. define RTO,RPO, maintain backups & replication & requlary test recovery.s
