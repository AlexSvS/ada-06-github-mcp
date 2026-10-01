# ADA-06: GitHub Model Context Protocol (MCP) Read-Only Engineering Review

An engineering audit and repository review project demonstrating the secure integration of the official remote **GitHub Model Context Protocol (MCP)** server with an AI-assisted development workflow (Google Antigravity / Gemini 3.8 Flash).

This project focuses on the principle of **least privilege**, **credential isolation**, and **read-only repository inspection**, evaluating the target repository [`AlexSvS/ada-05-spec-driven-feature`](https://github.com/AlexSvS/ada-05-spec-driven-feature).

---

## 📋 Table of Contents

- [Overview](#overview)
- [Repository Structure](#repository-structure)
- [MCP Configuration & Security Posture](#mcp-configuration--security-posture)
  - [Connection Details](#connection-details)
  - [Credential Isolation](#credential-isolation)
  - [Active Toolsets & Permission Control](#active-toolsets--permission-control)
- [Key Review Findings](#key-review-findings)
  - [Target Repository Architecture](#target-repository-architecture)
  - [Issue #2 Root-Cause Analysis](#issue-2-root-cause-analysis)
  - [Pull Request #1 Review & Defect Detection](#pull-request-1-review--defect-detection)
  - [Traceability Matrix](#traceability-matrix)
- [Security & Risk Analysis](#security--risk-analysis)
- [AI Usage & Human-in-the-Loop Governance](#ai-usage--human-in-the-loop-governance)
- [Document Index](#document-index)

---

## 🔍 Overview

The primary objective of **ADA-06** is to conduct a professional software engineering review of a remote repository using GitHub MCP without performing any write or mutation actions on GitHub.

Key goals achieved:
1. **Remote MCP Setup**: Configured the official remote GitHub MCP server (`https://api.githubcopilot.com/mcp/`) with header-enforced read-only operations.
2. **Architecture & Codebase Analysis**: Deep-dive inspection of a 3-tier Python CLI application (`customer_search`), including presentation, service, persistence, and domain layers.
3. **Issue & PR Triaging**: Full engineering analysis of open Issue #2 and PR #1 to diagnose test regressions and scope creep.
4. **Security & Permission Auditing**: Comprehensive inventory and classification of MCP tools, auditing granted vs. needed permissions, and establishing risk mitigation strategies.
5. **Human Oversight**: Continuous human verification of AI inferences against actual source code and git history.

---

## 📁 Repository Structure

```text
ada-06-github-mcp/
├── AI_USAGE_LOG.md                     # Comprehensive audit log of AI interactions, student decisions, and validation steps
├── README.md                           # Project documentation, architecture overview, and index
├── docs/
│   ├── MCP_GITHUB_PERMISSION_REVIEW.md # Fine-grained permission audit table and operational risk analysis
│   └── MCP_GITHUB_TOOL_INVENTORY.md    # Tool catalog detailing exposed GitHub MCP capabilities, scopes, and risk ratings
└── results/
    └── github-mcp-review.md            # Comprehensive final engineering review report for AlexSvS/ada-05-spec-driven-feature
```

---

## ⚙️ MCP Configuration & Security Posture

### Connection Details

The project utilizes the official remote GitHub MCP server endpoint:
- **Server URL**: `https://api.githubcopilot.com/mcp/`
- **Protocol Mode**: Strictly **READ-ONLY**
- **Authentication**: GitHub Fine-Grained Personal Access Token (PAT)

### Credential Isolation

To prevent credential leakage:
- No tokens, secrets, or environment files are stored within the project repository or committed to git.
- The PAT is configured in the global profile (`~/.gemini/config/mcp_config.json`).
- Repository `.gitignore` and secret scanning policies protect against accidental exposure.

### Active Toolsets & Permission Control

The MCP session limits access via protocol headers:
- `X-MCP-Readonly: true` — Completely disables write tools (create issue, push commit, merge PR, comment).
- `X-MCP-Toolsets: repos,issues,pull_requests` — Restricts tools to relevant domains.

```json
{
  "github-readonly": {
    "serverUrl": "https://api.githubcopilot.com/mcp/",
    "headers": {
      "Authorization": "Bearer <FINE_GRAINED_GITHUB_PAT>",
      "X-MCP-Readonly": "true",
      "X-MCP-Toolsets": "repos,issues,pull_requests"
    }
  }
}
```

---

## 📊 Key Review Findings

The review targeted [`AlexSvS/ada-05-spec-driven-feature`](https://github.com/AlexSvS/ada-05-spec-driven-feature), an offline customer search CLI application written in Python 3.11+.

### Target Repository Architecture

The reviewed system implements a clean **3-Tier Layered Architecture**:
1. **Presentation Layer (`src/customer_search/cli.py`, `__main__.py`)**: Standard library `argparse` CLI, formatting customer records and managing POSIX exit codes (`0` success, `1` validation error, `2` storage error).
2. **Business Logic Layer (`src/customer_search/service.py`)**: `CustomerService` enforcing input validation and case-insensitive substring search matching across names and email addresses.
3. **Data Persistence Layer (`src/customer_search/storage.py`)**: `CustomerStorage` handling local JSON serialization (`customers.json`), schema integrity verification, and custom exception encapsulation (`StorageError`).
4. **Domain Model (`src/customer_search/models.py`)**: Immutable dataclass (`Customer`) representing domain entities.

### Issue #2 Root-Cause Analysis

- **Issue Title**: *Fix incorrect package version expected by test_package_import*
- **Problem**: `test_package_import()` in `tests/test_setup.py` failed assertion against `customer_search.__version__`.
- **Root Cause**: Commit `f4b58c4` on branch `update-branch` updated the test expectation from `"0.1.0"` to `"0.2.0"`, but neither `src/customer_search/__init__.py` nor `pyproject.toml` were updated, creating an unresolved version conflict.

### Pull Request #1 Review & Defect Detection

- **PR Title**: *New requirements added*
- **Status**: Potential Defect / Scope Creep
- **Findings**:
  - **Defect**: Merging PR #1 breaks the CI/test pipeline because commit `f4b58c4` altered `tests/test_setup.py` without updating runtime version metadata.
  - **Unimplemented Specifications**: Requirements `FR-07`, `FR-08` (deterministic ordering), and `FR-09` were added to `REQUIREMENTS.md` with zero implementation code and zero test coverage.
  - **Unannounced Scope**: PR description indicated only documentation changes, hiding the breaking test modification.

### Traceability Matrix

Issue → Requirement/Spec → PR → Code → Test
|                            Issue/Need                            |   PR  |       Code / Files      |    Test / Evidence    | Estado |
|:----------------------------------------------------------------:|:-----:|:-----------------------:|:---------------------:|:------:|
| #2/Fix incorrect package version expected by test_package_import | PR #1 | src/tests/test_setup.py | test_package_import() | Gap    |

---

## 🛡️ Security & Risk Analysis

Full risk assessment documented in [MCP_GITHUB_PERMISSION_REVIEW.md](file:///c:/Users/Alex/Documents/agy2-projects/ada-06-github-mcp/ada-06-github-mcp/docs/MCP_GITHUB_PERMISSION_REVIEW.md):

| Risk Area | Threat Scenario | Applied Mitigation |
| :--- | :--- | :--- |
| **Unauthorized Remote Modifications** | Automated agent posts comments, creates issues, or pushes code. | Protocol-level read-only enforcement (`X-MCP-Readonly: true`) and zero write tools registered. |
| **Credential / Token Exposure** | GitHub PAT inadvertently committed to version control. | Tokens stored in global profile (`~/.gemini/config/mcp_config.json`) outside repository git tree. |
| **Excessive Permissions** | Broad token access with wide blast radius across repositories. | Fine-grained PAT scoped exclusively to target repository with minimum required scopes. |
| **Unintended Scope Access** | Agent searches or exfiltrates metadata across organizations. | Token restricted to "Only select repositories" and explicit instruction bounding. |

---

## 🤖 AI Usage & Human-in-the-Loop Governance

This repository adheres to strict AI governance and transparency. All interactions are audited in [AI_USAGE_LOG.md](file:///c:/Users/Alex/Documents/agy2-projects/ada-06-github-mcp/ada-06-github-mcp/AI_USAGE_LOG.md):

- **Entry 01**: Remote GitHub MCP Configuration & PAT validation.
- **Entry 02**: Read-Only Repository Inspection & Architectural Analysis.
- **Entry 03**: In-depth Issue #2 and PR #1 Triaging & Classification.
- **Entry 04**: Security Auditing, Tool Inventory, and Risk Assessment.

**Human Oversight**:
- The student manually verified all architectural assessments and git commits directly against the target repository.
- Hallucinated or non-existent tools were audited and excluded from documentation.
- AI inferences were cross-referenced against raw git diffs to confirm the causality between commit `f4b58c4` and Issue #2.

---

## 📑 Document Index

- 📄 **[results/github-mcp-review.md](file:///c:/Users/Alex/Documents/agy2-projects/ada-06-github-mcp/ada-06-github-mcp/results/github-mcp-review.md)** — Full engineering review report.
- 📄 **[docs/MCP_GITHUB_PERMISSION_REVIEW.md](file:///c:/Users/Alex/Documents/agy2-projects/ada-06-github-mcp/ada-06-github-mcp/docs/MCP_GITHUB_PERMISSION_REVIEW.md)** — Detailed permission audit and risk mitigation matrix.
- 📄 **[docs/MCP_GITHUB_TOOL_INVENTORY.md](file:///c:/Users/Alex/Documents/agy2-projects/ada-06-github-mcp/ada-06-github-mcp/docs/MCP_GITHUB_TOOL_INVENTORY.md)** — Tool capability catalog and risk classification.
- 📄 **[AI_USAGE_LOG.md](file:///c:/Users/Alex/Documents/agy2-projects/ada-06-github-mcp/ada-06-github-mcp/AI_USAGE_LOG.md)** — Complete step-by-step AI interaction and verification log.
