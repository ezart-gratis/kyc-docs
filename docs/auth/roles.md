# Role catalog, permissions matrix, enforcement

Delpoyment Contexts:

- **Internal (Transfer Gratis)**
- **Client Company Integration**
- **Saas Offering**

# 🔑 KYC App – Role Model & Access Control

## 📌 Overview

1. **Internal (Parent Company)** – used by Transfer Gratis to manage its own KYC flows.
2. **Client Company Integration** – integrated into a client’s existing KYC process as a plug-in service.
3. 3. **SaaS Model** – offered as a standalone multi-tenant platform for multiple client companies.

Access is controlled via **Keycloak** (OIDC provider). Roles determine what a user or service can do in the system.

## 👥 Roles

### 🔹 Super Admin (TransferGratis Only)

#### `kyc-super-admin` 🚨

- **Scope:** Available **only** to TransferGratis.
- **Responsibilities:**
  - Manage **all tenants/realms** (create, suspend, delete)
  - Define **system-wide templates & ML models**
  - Configure **global integrations** (OCR engines, sanction providers)
  - Oversee **billing & subscription plans** (for SaaS)
  - Escalated access for troubleshooting
- **Who uses it?**
  - Internal platform owners
  - DevOps / Engineering leads
  - Security administrators

---

### 🔹 Shared Roles (TransferGratis, Clients, SaaS Tenants)

#### `kyc-admin`

- **Scope:** TransferGratis, client companies, SaaS tenants.
- **Responsibilities:**
  - Manage templates & components **within their tenant**
  - Configure tenant-specific integrations (e.g., webhook to client CRM)
  - Create/manage tenant users and roles
  - Full CRUD inside **their tenant only**
- **Who uses it?**
  - TransferGratis compliance leads
  - Client compliance admins
  - SaaS tenant admins

---

#### `kyc-analyst`

- **Scope:** Tenant-scoped.
- **Responsibilities:**
  - Perform KYC checks (document review, verification)
  - Reorder components in templates
  - Approve/reject submissions
- **Who uses it?**
  - TransferGratis KYC staff
  - Client compliance officers
  - SaaS tenant analysts

---

#### `kyc-viewer`

- **Scope:** Tenant-scoped.
- **Responsibilities:**
  - Read-only visibility of workflows
  - Access reporting dashboards & audit logs
- **Who uses it?**
  - Auditors
  - Management staff (visibility only)

---

#### `kyc-service`

- **Scope:** Tenant-scoped (per company/tenant).
- **Responsibilities:**
  - Service accounts for API-to-API integration
  - Trigger OCR, sanctions, and ML pipelines
  - Sync data with external systems
  - Used by client applications (e.g., banking apps, TransferGratis) to call the KYC Hub on behalf of their end-users.
- **Who uses it?**
  - Worker services (Celery/Dramatiq, etc.)
  - External integrations (CRMs, banking APIs)

---

## 📂 Deployment Contexts

### 🔹 TransferGratis (Internal)

- `kyc-super-admin` ✅ (exclusive to TransferGratis)
- `kyc-admin`
- `kyc-analyst`
- `kyc-viewer`
- `kyc-service`

### 🔹 Client Company Integration

- `kyc-admin`
- `kyc-analyst`
- `kyc-viewer`
- `kyc-service`  
  ❌ **No `kyc-super-admin`**

### 🔹 SaaS Multi-Tenant

- `kyc-super-admin` ✅ (TransferGratis only, cross-tenant oversight)
- `kyc-admin` (per tenant)
- `kyc-analyst` (per tenant)
- `kyc-viewer` (per tenant)
- `kyc-service` (per tenant)

---

## ✅ Summary

- **TransferGratis** has **exclusive super-admin rights** (`kyc-super-admin`) across the platform.
- **Client companies** and **SaaS tenants** get only tenant-scoped roles (`kyc-admin`, `kyc-analyst`, `kyc-viewer`, `kyc-service`).
- This ensures **least privilege**, **multi-tenancy security**, and **clear separation of responsibilities**.
