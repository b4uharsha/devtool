Flow Summary

Developer runs terraform apply locally or commits to GitHub.

GitHub Actions (terraform-ci.yml) triggers provisioning.

Terraform creates VM + remote state backend.

Ansible runs automatically to install tools.

Services (Jenkins, Grafana, Prometheus, etc.) become accessible.

Outputs show IP, URLs, kubeconfig paths, and credentials.

                         
                         ┌─────────────────────────────┐
                         │     Developer Laptop        │
                         │─────────────────────────────│
                         │ • Terraform CLI             │
                         │ • terraform.tfvars toggles  │
                         └────────────┬────────────────┘
                                      │
                         (git push / terraform apply)
                                      │
        ┌─────────────────────────────┴─────────────────────────────┐
        │                   GitHub Actions CI/CD                    │
        │──────────────────────────────────────────────────────────│
        │ • terraform-ci.yml  → validate / plan / apply             │
        │ • ansible-run.yml   → run Ansible playbooks               │
        │ • GitHub Secrets (AWS/GCP creds, SSH keys)                │
        └────────────┬──────────────────────────────────────────────┘
                     │
                     │ (Terraform Plan / Apply via workflows)
                     │
        ┌────────────┴──────────────────────────┐
        │          Cloud Environment            │
        │ (AWS or GCP Infrastructure Layer)     │
        │───────────────────────────────────────│
        │ • EC2 or GCE Instance (DevOps VM)     │
        │ • Security Groups / Firewalls         │
        │ • Remote State: S3+DynamoDB or GCS    │
        └────────────┬──────────────────────────┘
                     │
                     │ (Ansible SSH / SSM Provisioning)
                     │
        ┌────────────┴───────────────────────────────┐
        │             DevOps VM (Single Host)         │
        │────────────────────────────────────────────│
        │ Installed Tools (based on enabled modules): │
        │   • Docker                                  │
        │   • Kubernetes (kubectl + kind/minikube)    │
        │   • Jenkins                                 │
        │   • Prometheus + Grafana                    │
        │   • Git, Python, Terraform CLI              │
        │   • AWS CLI / GCP CLI                       │
        │   • k9s, VS Code Server, Postman CLI        │
        │   • Java, Maven, Gradle                     │
        │   • Ansible (local execution for config)    │
        │---------------------------------------------│
        │ Exposed Services:                           │
        │   • Jenkins → http://<vm-ip>:8080           │
        │   • Grafana → http://<vm-ip>:3000           │
        │   • Prometheus → http://<vm-ip>:9090        │
        └─────────────────────────────────────────────┘

