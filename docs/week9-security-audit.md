# Week 9 Security Audit — cis410-deploy-sa

**Project:** <!-- CIS410-sahil -->
**Date:** <!-- 05/31/2026 -->
**Auditor:** <!-- Sahil Shirzad -->

---

## 1. IAM Audit Results

### Before — Week 8 Configuration (over-permissioned)

| Role | Scope | Problem |
|---|---|---|
| roles/run.admin | Project | Overly broad — grants ability to delete services and modify IAM, not just deploy |
| roles/storage.admin | Project | Overly broad — grants access to ALL GCS buckets in the project |
| roles/artifactregistry.writer | Project | Acceptable — scoped to push images only |
| roles/viewer | Project | Acceptable — read-only project metadata |
| roles/iam.serviceAccountUser | Compute SA | Required — needed to act as Compute Engine default SA |

### After — Week 9 Least-Privilege Fix

| Role | Scope | Why Sufficient |
|---|---|---|
| roles/run.developer | Project | Deploy only — cannot delete services or modify IAM |
| roles/storage.admin | tfstate bucket only | Scoped to one bucket — not all storage |
| roles/artifactregistry.writer | Project | Unchanged — push images only |
| roles/viewer | Project | Unchanged — read project metadata |
| roles/iam.serviceAccountUser | Compute SA | Unchanged — required for Cloud Run deployment |

---

## 2. Secret Manager Migration

- **Secret created:** `flask-app-secret`
- **Replication:** automatic
- **Access granted to:** `cis410-deploy-sa` — roles/secretmanager.secretAccessor on this secret only
- **Access granted to:** `PROJECT_NUMBER-compute@developer.gserviceaccount.com` — roles/secretmanager.secretAccessor on this secret only (required for Cloud Run runtime access)
- **Cloud Run update:** APP_SECRET environment variable mounted from Secret Manager at runtime

---

## 3. Monitoring Configuration

- **Log-based alert:** `cis410-flask-app-alert` — fires on severity>=WARNING for cis410-flask-app
- **Notification channel:** <!-- sahil786@students.highline.edu -->
- **Billing budget:** `cis410-monthly-budget` — $20 limit, alerts at 50% / 90% / 100%

---

## 4. Reflection

**Q1: Why is roles/run.admin inappropriate for a CI/CD pipeline service account?**

<!-- The roles/run.admin role provides permissions beyond what a CI/CD pipeline requires. It allows deleting services, modifying IAM policies, and changing traffic routing. Using roles/run.developer follows the principle of least privilege while still allowing deployments. -->

---

**Q2: What is the security difference between storing a secret in GitHub Secrets vs. Google Secret Manager?**

<!-- GitHub Secrets store secrets for workflows but provide limited auditing and access control. Google Secret Manager offers centralized secret management, versioning, detailed audit logs, and fine-grained IAM permissions. This makes Secret Manager more secure for production environments. -->

---

**Q3: A coworker says "I will clean up IAM permissions after the project launches. For now I need everything to work fast." What is the risk of this approach?**

<!-- Delaying IAM cleanup increases security risk because service accounts may keep excessive permissions longer than necessary. If an account is compromised, attackers could misuse those permissions to access or modify resources. Applying least privilege early reduces the attack surface. -->
