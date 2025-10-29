---

# 🧭 **Epic: Modular DevOps Environment Provisioning with Terraform**

> **Goal:**
> Build a reusable, modular, and cloud-agnostic DevOps environment on AWS or GCP using Terraform, Ansible, and GitHub Actions — with full plug-and-play control for all tools.

---

## 🧱 **Phase 1: Foundation Setup**

### **Task 1: Initialize Terraform Project**

* Create root structure: `main.tf`, `variables.tf`, `outputs.tf`, `providers.tf`.
* Configure providers for both **AWS** and **GCP**.
* Add backend configuration (`backend_s3.tf` and `backend_gcs.tf`) for remote state.
* Create `.gitignore`, `.terraform.lock.hcl`, and `README.md`.

**Deliverables:**

* Working Terraform skeleton
* Cloud provider selection via variable (`cloud = "aws" or "gcp"`)

---

### **Task 2: Create VM Module**

* Create `modules/vm/` to provision:

  * **EC2 (AWS)** or **GCE (GCP)** instance
  * Security group / firewall
  * SSH key pair or SSM access
* Output: `public_ip`, `public_dns`, `instance_id`.

**Deliverables:**

* Modular `vm` folder
* Verified VM provisioning on both clouds

---

### **Task 3: Define Core Variables & Toggles**

* Add `enable_<tool>` variables in `variables.tf` for every component:

  * Docker, Python, Git, Terraform, Kubernetes, Jenkins, Prometheus, Grafana, etc.
* Default to `true` for core tools, `false` for optional ones.
* Create sample `terraform.tfvars.example`.

**Deliverables:**

* Centralized variable configuration
* Example toggle file

---

## ⚙️ **Phase 2: Tool Modules**

### **Task 4: Create Individual Tool Modules**

For each tool, create a sub-module under `/modules/<tool>/`:

* `main.tf`, `variables.tf`, `outputs.tf`
* Use either:

  * `local-exec` to call scripts, or
  * Hook into **Ansible roles** for configuration

**Modules to create:**

1. Docker
2. Python
3. Git
4. Terraform CLI
5. Kubernetes (kubectl + kind/minikube)
6. Jenkins
7. Prometheus + Grafana
8. k9s
9. VS Code Server
10. Postman CLI
11. AWS CLI / GCP CLI
12. Ansible
13. Java
14. Maven
15. Gradle

**Deliverables:**

* Independent Terraform modules for each tool
* `count = var.enable_<tool> ? 1 : 0` toggle support

---

### **Task 5: Create Installation Scripts**

* Add shell scripts under `/scripts/` for base installation of each tool.
* Each script should:

  * Handle idempotent install (check before install).
  * Log installation steps.
  * Exit gracefully on errors.

**Deliverables:**

* `scripts/install_<tool>.sh` for all tools
* Verified manual execution on Ubuntu VM

---

## 🔧 **Phase 3: Configuration Automation**

### **Task 6: Integrate Ansible**

* Create `/ansible/` directory with:

  * `playbooks/site.yml`
  * Roles for each tool (mirror Terraform modules)
* Configure inventory file generation dynamically (from Terraform outputs).
* Add verification playbook (`verify.yml`).

**Deliverables:**

* Working Ansible setup for post-provision configuration
* Each role installs/configures a single tool

---

### **Task 7: Build Ansible Driver Module**

* Add `modules/ansible_driver/` to:

  * Generate Ansible inventory with VM IP.
  * Compose tags dynamically from enabled tools.
  * Execute Ansible with `local-exec`:

    ```bash
    ansible-playbook -i inventories/default.ini playbooks/site.yml --tags "<enabled-tools>"
    ```
* Verify it triggers automatically post `terraform apply`.

**Deliverables:**

* Automated Terraform → Ansible integration
* Verified tag-based installation

---

## 🧠 **Phase 4: CI/CD Pipeline Setup**

### **Task 8: Setup GitHub Actions for Terraform**

* Create `.github/workflows/terraform-ci.yml`:

  * Validate Terraform (`fmt`, `validate`)
  * Plan on PR
  * Apply on manual trigger
* Add secrets for AWS/GCP credentials.

**Deliverables:**

* Terraform workflow with plan/apply stages
* CI runs on PRs and manual triggers

---

### **Task 9: Setup GitHub Actions for Ansible**

* Create `.github/workflows/ansible-run.yml`:

  * Setup Python & Ansible
  * Pull inventory from Terraform outputs
  * Run playbooks with dynamic tags
* Optionally trigger automatically after successful Terraform apply.

**Deliverables:**

* Ansible GitHub Actions workflow
* Validated role execution through pipeline

---

## 📊 **Phase 5: Outputs & Validation**

### **Task 10: Configure Outputs**

* Add outputs for:

  * VM IP / DNS
  * Jenkins URL
  * Grafana & Prometheus URLs
  * Kubeconfig path
  * Any credentials generated
* Display in `terraform output` post-deployment.

**Deliverables:**

* Output configuration in `outputs.tf`
* Valid URLs printed after deployment

---

### **Task 11: Verification & Testing**

* Test `terraform apply` end-to-end:

  * Deploy with all tools enabled.
  * Re-run with selective toggles (e.g., disable Jenkins).
  * Validate outputs and service accessibility.
* Test Ansible playbooks individually.

**Deliverables:**

* Validated deployment with all tools.
* Verified modular toggling functionality.

---

## 🚀 **Phase 6: Documentation & Enhancements**

### **Task 12: Update README.md**

---

## ✅ **Summary Deliverables**

| Area           | Deliverable                                 |
| -------------- | ------------------------------------------- |
| Terraform      | Modular environment with toggles            |
| Ansible        | Config-driven installation and verification |
| GitHub Actions | Automated Terraform & Ansible workflows     |
| Documentation  | README + variable reference                 |
| Outputs        | URLs, IPs, configs post-deployment          |

---

