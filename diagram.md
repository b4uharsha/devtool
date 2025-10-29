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


┌────────────────────────────────────────────────────────────────────┐
│                 Provisioning Workflow (End-to-End)                 │
└────────────────────────────────────────────────────────────────────┘

[1] Developer
    ├─ Edits terraform.tfvars (enable_* toggles)
    ├─ Runs `terraform init && terraform plan && terraform apply`
    └─ (Optionally) pushes to repo → triggers CI

        │
        ▼
[2] GitHub Actions (optional path)
    ├─ Workflow: terraform-ci.yml
    │    ├─ terraform fmt / validate
    │    ├─ terraform init (remote state: S3+DynamoDB or GCS)
    │    ├─ terraform plan
    │    └─ terraform apply (manual or protected)
    └─ Secrets: cloud creds, SSH/SSM keys

        │
        ▼
[3] Terraform (Provisioning)
    ├─ Selects provider (AWS/GCP) from variables
    ├─ Creates network + security group / firewall
    ├─ Creates VM (EC2/GCE) and base bootstrap (cloud-init)
    ├─ Outputs VM IP/DNS and connection details
    └─ Writes state to remote backend (S3/GCS + lock)

        │
        ▼
[4] Ansible Orchestration (triggered by TF)
    ├─ Terraform renders inventory with VM IP
    ├─ Terraform runs ansible-playbook (via local-exec/runner)
    ├─ Tags are computed from enable_* toggles
    └─ Roles executed:
         • docker, kubernetes (kubectl + kind/minikube)
         • jenkins
         • prometheus + grafana
         • git, python, terraform CLI
         • aws/gcp cli, k9s, vscode server, postman
         • java, maven, gradle

        │
        ▼
[5] Configuration & Health Checks (on VM)
    ├─ Systemd services enabled & started
    ├─ Ports opened (SG/Firewall): 8080, 3000, 9090 (etc.)
    ├─ Sanity checks per tool (curl/cli probes)
    └─ Fail-fast on errors with clear logs

        │
        ▼
[6] Outputs to Developer / CI Logs
    ├─ vm_public_ip / vm_public_dns
    ├─ jenkins_url:   http://<ip>:8080
    ├─ grafana_url:   http://<ip>:3000
    ├─ prometheus:    http://<ip>:9090
    ├─ kubeconfig:    /home/<user>/.kube/config
    └─ Any generated credentials or notes

        │
        ▼
[7] Usage
    ├─ SSH/SSM into VM (if needed)
    ├─ `kubectl get nodes` (kind/minikube context)
    ├─ Access Jenkins/Grafana/Prometheus via URLs
    └─ Toggle tools by updating terraform.tfvars → re-apply

