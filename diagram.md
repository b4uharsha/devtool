flowchart TD
    A[Developer Laptop] -->|Run Terraform locally or push to GitHub| B[GitHub Actions CI/CD]

    subgraph GitHub["GitHub Actions Workflows"]
        B1[terraform-ci.yml<br/>→ terraform fmt / validate / plan / apply]
        B2[ansible-run.yml<br/>→ ansible-playbook execution]
    end

    B -->|Uses secrets & providers| C[(AWS / GCP Cloud)]

    subgraph Cloud["Cloud Environment (AWS or GCP)"]
        C1[EC2 / GCE Instance<br/>DevOps VM]
        C2[S3 + DynamoDB / GCS<br/>Terraform Remote State]
        C3[Security Group / Firewall]
    end

    C1 -->|Ansible Roles / Provisioners| D[Installed Tools & Services]

    subgraph D["DevOps VM Components"]
        D1[Docker]
        D2[Kubernetes<br/>(kubectl + kind/minikube)]
        D3[Jenkins (8080)]
        D4[Prometheus (9090)]
        D5[Grafana (3000)]
        D6[Python / Git / Terraform CLI]
        D7[AWS CLI / GCP CLI]
        D8[k9s / VS Code Server / Postman CLI]
        D9[Java / Maven / Gradle]
    end

    C1 -->|SSH / SSM| D
    D4 --> D5
    D2 --> D1
