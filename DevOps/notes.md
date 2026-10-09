# what a DevOps ?

Devops is a combination of development & operations practices. The main goal of the devops is to make software delivery faster, more reliable and more automated.

In traditional approach, developer teams write code and then operations teams will manually deploy and maintain it. so, Devops tries to bring together these teams and process closer together through collaboration, automation, CI/CD, Infrastructure as code, monitoring and continous feedback.

    Developer -------> Git -------> CI/CD -------> build/test -------> deploy -------> monitoring -------> feedback

when, a developer pushes code to the Git, then CI/CD pipelines can automatically build & test the application and then deploy it to the required environment.

So in simpler terms, in which i understand devOps as a collaborations between deployments and operations supported by automation and continuous feedback to deliver & operate software reliably.

others types of Ops

# 2. GitOps

A modern yet specific way of doing devops for infrastructure & kubernetes-style deployment. Git is the single source of truth in this deployment. An agent inside the cluster (argo cd, flux) continuously compares what's running against Git & reconciles any differences (self healing). This is a pull-base method

(drift is a term used in gitops to explain a situation where a manual code or infrastructure is change but the git doesn't get merges to the changes which create conflict the git and desired state so this state what a drift.)

key consequences:
-> Deploy = merge a pull request where as Rollback = git revert.
-> Manual changes to the cluster get automatically reverted (drift correction)

# 3. MlOps

CI/CD specifically for AI/ML. It doesn't just test code: it test data quality & model accuracy. Automates retraining and performance monitoring.

-> data changes (new data, drift, bad labels)
-> Models are trained artifacts, not compiled ones.

limitation: Massive infrastructure costs & complex data versioning

# Comparision

| Category                    | DevOps                                                                         | GitOps                                                                             | AIOps                                                                            |
| --------------------------- | ------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| **Core idea**               | Dev + Ops collaboration through automation                                     | Git is the source of truth; automation keeps the system in sync                    | Use AI/ML to analyze and automate IT operations                                  |
| **Deploys**                 | Application code, infrastructure, and configurations                           | Applications + infrastructure/configuration defined in Git                         | Not primarily focused on deployment; focuses on operating and optimizing systems |
| **What's versioned?**       | Code, infrastructure, and CI/CD configuration                                  | Desired state: application configs, Kubernetes manifests, infrastructure, policies | Mainly analyzes operational data such as logs, metrics, events, and alerts       |
| **Extra problem it solves** | Reduces manual work and Dev/Ops silos; makes delivery faster and more reliable | Prevents configuration drift; provides auditability and easy rollback through Git  | Detects anomalies, reduces alert noise, and helps identify root causes           |
| **Typical tools**           | Jenkins, GitHub Actions, GitLab CI, Docker, Kubernetes, Terraform              | Argo CD, Flux, Crossplane, Terraform                                               | Dynatrace, Splunk, Datadog, New Relic, BigPanda                                  |
