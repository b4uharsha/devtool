# 🧭 **Task: Provision Modular DevOps Environment Using Terraform (with Ansible & GitHub Actions)**

## 🎯 **Objective**

Create a **fully modular, plug-and-play DevOps environment** on **AWS** or **GCP** using **Terraform (executed locally)**.
The setup must allow **each tool** to be **enabled or disabled independently** using Terraform variables.

---

## 🧱 **Scope of Work**

### 1️⃣ **Infrastructure Provisioning**

* Use **Terraform** to provision a **single VM**:

  * **AWS:** EC2 instance
  * **GCP:** GCE instance
* The VM should serve as a **self-contained DevOps environment**.
* Provision network, SSH/SSM access, and security groups/firewalls.

---

### 2️⃣ **Modular Architecture**

* Design the Terraform project in a **modular structure** (`/modules/<tool>`) for plug-and-play configuration.
* Each module installs/configures one tool and can be **toggled on/off** with `enable_<tool>` flags in `terraform.tfvars`.

---

### 3️⃣ **Tools to Include (All Modular / Toggleable)**

#### 🔹 Core System & Infrastructure

* Docker
* Python
* Git
* Terraform CLI
* Kubernetes (`kubectl`, `kind`, or `minikube`)
* AWS CLI or GCP CLI

#### 🔹 CI/CD & Automation

* Jenkins
* Ansible

#### 🔹 Monitoring & Observability

* Prometheus + Grafana

#### 🔹 Developer & Utility Tools

* k9s – Kubernetes TUI
* VS Code Server – Web-based IDE
* Postman CLI (Newman) – API testing automation
* Java
* Maven
* Gradle

---

### 4️⃣ **Automation & Configuration**

#### 🧩 Terraform

* Each tool’s installation and configuration handled via:

  * **Terraform provisioners**, or
  * **Ansible roles** triggered by Terraform (`modules/ansible_driver`).
* Use `count = var.enable_<tool> ? 1 : 0` to toggle resources dynamically.

#### ⚙️ Ansible Integration

* Maintain an **Ansible directory** with roles for each tool.
* After VM creation, Terraform runs Ansible playbooks automatically using:

  * Inventory generated from VM outputs.
  * Tags matching the enabled tools.
* Ansible should:

  * Install each selected tool.
  * Configure startup services.
  * Perform post-install verification.

#### 🚀 GitHub Actions Integration

* Add CI/CD workflows under `.github/workflows/`:

  1. **terraform-ci.yml**

     * Performs `terraform fmt`, `init`, `validate`, `plan`, `apply`.
  2. **ansible-run.yml**

     * Optionally executes Ansible playbooks after Terraform deployment.
* Support manual and PR triggers.
* Store credentials securely via GitHub Secrets:

  * `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `GOOGLE_CREDENTIALS`, `VM_SSH_PRIVATE_KEY`, etc.

---

### 5️⃣ **Outputs**

After successful deployment, Terraform should output:

* VM Public IP and DNS
* Jenkins URL
* Grafana and Prometheus URLs
* Kubeconfig path
* User credentials if applicable

---

### 6️⃣ **Deliverables**

1. **Terraform codebase** with modular structure:

   * `main.tf`, `variables.tf`, `outputs.tf`, `providers.tf`
   * `/modules/` for each tool
   * `/scripts/` for installation
   * `/ansible/` with playbooks and roles
   * `.github/workflows/` for Terraform + Ansible CI pipelines
2. **README.md** explaining:

   * Folder structure
   * Variable toggles
   * Deployment commands
   * Expected outputs

---

### 7️⃣ **Acceptance Criteria**

✅ One command (`terraform apply`) provisions a VM with selected tools.
✅ Each tool can be independently enabled or disabled in `terraform.tfvars`.
✅ Ansible automatically configures and verifies installed tools.
✅ GitHub Actions validates and applies Terraform plans automatically.
✅ Outputs display service URLs (Jenkins, Grafana, Prometheus, etc.).

---

### 8️⃣ **Reference Folder Structure**

```plaintext
terraform-devops-env/
├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── terraform.tfvars
├── scripts/
├── ansible/
│   ├── inventories/
│   ├── playbooks/
│   └── roles/
├── modules/
│   ├── vm/
│   ├── docker/
│   ├── jenkins/
│   ├── prometheus_grafana/
│   ├── kubernetes/
│   ├── ansible_driver/
│   ├── vscode/
│   ├── postman/
│   ├── java/
│   ├── maven/
│   └── gradle/
└── .github/
    └── workflows/
        ├── terraform-ci.yml
        └── ansible-run.yml
```

---

### 9️⃣ **Goal**

To create a **reusable, cloud-agnostic DevOps lab** that can spin up a fully functional environment on AWS or GCP in minutes — fully automated via Terraform, configured via Ansible, and validated via GitHub Actions.

---
