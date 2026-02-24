# qTest + Tosca Cloud + GitHub Actions

A ready-to-use integration that runs **Tosca Cloud** playlists from **GitHub Actions**, captures **JUnit** results, and delivers them into **qTest** via **Pulse**—with no code changes required for your environment.

**Audience:** Solution architects who need to replicate this pipeline in their own qTest, Tosca Cloud, and GitHub setup. All configuration is done through **secrets** and **variables**; adapt the workflow by updating values, not editing code.

---

## What This Integration Does

| Step | What happens |
|------|----------------|
| 1 | GitHub Actions workflow runs (manually or triggered by a Pulse Rule). |
| 2 | Workflow authenticates to Tosca Cloud and starts the configured playlist. |
| 3 | Workflow polls until the run completes, then downloads JUnit results. |
| 4 | JUnit file is uploaded as a **workflow artifact** (for debugging and history). |
| 5 | The same results are sent to **qTest Pulse**; the parser updates qTest with test logs. |

Result: Tosca test runs are visible in qTest with pass/fail and step-level detail, and artifacts are stored in GitHub for each run.

---

## Prerequisites Overview

Before running the workflow, complete setup in four areas:

1. **qTest** — Project with automation enabled and status mappings configured.
2. **qTest Pulse** — Workspace with Rules imported and Constants set (token, Manager URL).
3. **Tosca Cloud** — Client ID/secret, Cloud URL, Auth URL, and Playlist ID from Swagger/UI.
4. **GitHub** — Repository with the workflow and all required secrets and variables.

Each section below gives the exact steps and values.

---

## 1. qTest Setup

- You need an **existing qTest project**.
- **Turn on Automation Integration** for that project:  
  [Automation Settings](https://docs.tricentis.com/qtest-saas/content/manager/settings/automation_settings.htm) — enable automation for the project.
- **Add Automation Status Mappings** (values are **case sensitive**):

  | Automation Status | qTest Status |
  |-------------------|--------------|
  | `succeeded`       | Passed       |
  | `failed`          | Failed       |

---

## 2. qTest Pulse Setup

- In the **qTest Pulse workspace** that corresponds to your qTest project:
  - **Import the Rules** from the [Pulse Rules](https://github.com/Andres-Tricentis/qTest-ToscaCloud-Integration/tree/main/Pulse%20Rules) folder in this repo.
  - After import, open **Constants** and set:

  | Constant    | Value |
  |-------------|--------|
  | **QTEST_TOKEN** | Your personal **Bearer token** from qTest: **Downloads** → **API & SDK**. ([Get token](https://docs.tricentis.com/qtest-saas/content/overview/download_qtest_resources_page.htm)) |
  | **ManagerURL**   | Base qTest Manager domain **without** protocol (e.g. `sademo.qtestnet.com`). The script uses HTTPS. |

- The **Pulse webhook URL** you will need for GitHub is found in Pulse by opening the trigger named **GitHub_Actions_Pipeline**; copy that URL for the `PULSE_WEBHOOK_URL` secret (see GitHub Setup below).

---

## 3. Tosca Cloud Setup

Gather these values from Tosca Cloud Swagger and UI:

| What to get | Where / how |
|-------------|-------------|
| **Client ID & Client Secret** | [Get client ID and secret](https://docs.tricentis.com/tosca-cloud/en-us/content/admin_guide/get_client_secret.htm) |
| **Tosca Cloud URL, E2G Auth URL, Playlist ID** | [Integrate CI/CD tools](https://docs.tricentis.com/tosca-cloud/en-us/content/admin_guide/integrate_ci_tools.htm#Get_token_URL) — follow the steps for token URL, Tosca Cloud URL, and playlist ID. |

Keep these handy; you will add them as GitHub secrets in the next section.

---

## 4. GitHub Setup

- Go to the repository that contains this workflow → **Settings** → **Secrets and variables** → **Actions**.

### Repository secrets (exact names required)

The workflow references these **exact** secret names. Add each with your own values:

| Secret | Description |
|--------|-------------|
| `TOSCA_CLIENT_ID` | Tosca Cloud client ID from Swagger. |
| `TOSCA_CLIENT_SECRET` | Tosca Cloud client secret. |
| `TOSCA_CLOUD_URL` | Tosca Cloud workspace URL (e.g. `https://<tenant>/<workspace_id>`). |
| `TOSCA_E2G_AUTH_URL` | Token URL from Swagger (E2G Auth URL). |
| `TOSCA_PLAYLIST_ID` | Playlist ID from the Tosca Cloud playlist edit URL. |
| `PULSE_WEBHOOK_URL` | In Pulse, open the trigger **GitHub_Actions_Pipeline** and copy the webhook URL. |

### Repository variables (where results go in qTest)

These tell the workflow **which qTest project and test cycle** to use for the results:

| Variable | Description |
|----------|-------------|
| `QTEST_PROJECT_ID` | qTest project ID (numeric) where results will be stored. |
| `QTEST_TEST_CYCLE_ID` | qTest Test Cycle ID (numeric) where results will be stored. |

> **Tip:** Use **Variables** (not Secrets) for project and cycle IDs so your team can change them without touching sensitive credentials.

---

## Next Steps

- **Run the workflow** once manually from the **Actions** tab to confirm end-to-end: Tosca run → JUnit artifact → qTest updated.
