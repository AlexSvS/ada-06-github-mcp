### Entry 01 — Remote GitHub MCP Configuration (Read-Only)

Tool: Antigravity (Gemini 3.8 Flash)
Date: 2026-09-26
Stage: MCP configuration

Prompt:
"We'll do an exercise, but first, we'll configure GitHub MCP in Antigravity. We'll be using the official remote GitHub MCP Server. Configure it in your global profile to avoid storing the token within the repository (Antigravity uses serverUrl for remote MCP servers)." / "i configured the mcp_config.json, check the file"

AI contribution:
Configured the global MCP profile (~/.gemini/config/mcp_config.json) with the official remote GitHub MCP server endpoint (https://api.githubcopilot.com/mcp/), established the read-only headers (X-MCP-Readonly: true, X-MCP-Toolsets: repos,issues,pull_requests), inserted the PAT, and validated the JSON syntax and security posture after student configuration.

Student decision:
The student chose to store the GitHub credentials globally in ~/.gemini/config/mcp_config.json rather than in the project repository to prevent committing secrets to VCS. Configured and reviewed the final configuration file directly.

Impact:
Successfully enabled secure, read-only GitHub MCP access without risk of leaking credentials in the git repository.


### Entry 02 — Read-Only Repository Inspection via GitHub MCP

Tool: Antigravity (Gemini 3.8 Flash)
Date: 2026-09-26
Stage: Repository analysis

Prompt:
"Using the GitHub MCP server, inspect repository:
AlexSvS/ada-05-spec-driven-feature

This is a READ-ONLY engineering analysis.
Do not modify GitHub.
Analyze:
1. Repository purpose.
2. Main directories and important files.
3. Application entry point(s).
4. Main components/modules.
5. Test organization.
6. Relevant documentation.
7. Potential areas affected by the selected Issue/PR.
For every GitHub MCP action, keep the scope limited to this repository.
Report which MCP capabilities you used."

AI contribution:
Conducted a comprehensive read-only engineering analysis of the AlexSvS/ada-05-spec-driven-feature repository using GitHub MCP tools (get_file_contents, list_issues, issue_read, list_pull_requests, pull_request_read, list_branches, list_commits). Analyzed the repository purpose, 3-tier architecture (CLI, service, storage, domain models), execution entry points, test suite organization, and specification docs. Evaluated open PR #1 and Issue #2 to uncover the package version mismatch (0.1.0 vs 0.2.0) and assess impact from new functional requirements (FR-07 through FR-09).

Student decision:
The student scoped and constrained the inspection to a strictly read-only analysis limited solely to the target repository. They validated the structural breakdown, testing organization, and identified impact on versioning and deterministic ordering by doing a deep review of the repository. In other words, the student read the original code and compared it with the results from the coding agent.

Impact:
N/A

### Entry 03 — Issue and Pull Request Analysis via GitHub MCP

Tool: Antigravity (Gemini 3.8 Flash)
Date: 2026-09-26
Stage: Issue / PR analysis

Prompt:
"Using GitHub MCP, read Issue #2 in AlexSvS/ada-05-spec-driven-feature. Do not modify the Issue. Analyze it as a software engineer: 1. Problem / requested behavior. 2. Expected behavior. 3. Acceptance information explicitly present. 4. Ambiguities or missing information. 5. Relevant code areas in the repository. 6. Existing tests related to the issue. 7. Risks or edge cases. 8. If REQUIREMENTS.md or SPEC.md exists, identify related IDs/sections. Separate facts from the Issue from your engineering inferences. Do not invent product decisions."

"Using GitHub MCP, inspect Pull Request #1 in AlexSvS/ada-05-spec-driven-feature. READ ONLY. Do not comment, approve, merge, edit or close the PR. Inspect: PR title and description, changed files, diff / code changes, commits, tests changed or added, review comments if available. Evaluate: 1. What behavior changes? 2. Which Issue / requirement appears to be addressed? 3. Which files implement the change? 4. Are tests present for the behavior? 5. Are there obvious gaps between the Issue and implementation? 6. Is there unrelated scope? 7. What should a human reviewer verify manually? Classify findings as: OBSERVATION, RISK, QUESTION, POTENTIAL DEFECT. Do not post anything to GitHub."

AI contribution:
Conducted an in-depth read-only analysis of Issue #2 and Pull Request #1 in AlexSvS/ada-05-spec-driven-feature using GitHub MCP tools (issue_read, pull_request_read). For Issue #2, separated explicit issue facts from engineering inferences, pinpointed the version mismatch causing test failure, analyzed missing acceptance criteria, and mapped relevant code areas and requirements (C-02, NFR-02, Task T-01). For PR #1, examined the diff, commits, changed files, and reviews; identified that new requirements (FR-07 to FR-09) lacked implementation and test coverage, detected that commit f4b58c4 introduced an unannounced test change that broke test execution, and categorized findings into OBSERVATION, RISK, QUESTION, and POTENTIAL DEFECT.

Student decision:
The student valuated the findings, validated the defect classification and scope gaps, and noted the causality between PR #1 commit f4b58c4 and Issue #2 by checking manually the original code. Then the student filled the github-mcp-review.md with the correct information provided by the agent.

Impact:
N/A

### Entry 04 — GitHub MCP Permissions Review and Risk Analysis

Tool: Antigravity (Gemini 3.8 Flash)
Date: 2026-09-26
Stage: Permission/security review

Prompt:
"Inspect the GitHub MCP capabilities available in this session. Do not execute any write action. Question: Could you create an Issue, comment on a Pull Request, or modify repository files using the currently exposed MCP tools? Explain your answer using the actual tools available. Do not attempt the write operation." / "Add a brief risk analysis to MCP_GITHUB_PERMISSION_REVIEW.md in the form of a table with the following information: - Risk - An example - Mitigation"

AI contribution:
Audited the exposed GitHub MCP tools, schemas, and global configuration headers (X-MCP-Readonly: true, X-MCP-Toolsets), formally verifying that all issue, PR, and repository write/mutation capabilities were omitted. Formulated and appended a structured Risk Analysis table to docs/MCP_GITHUB_PERMISSION_REVIEW.md detailing risks (unauthorized remote modifications, credential leakage, excessive token scope, and unintended repository access), concrete examples, and operational mitigations.

Student decision:
The student audited the active MCP tools and configurations to confirm read-only enforcement, verified the permission review findings, and instructed the agent to document key operational risks and mitigation controls in docs/MCP_GITHUB_PERMISSION_REVIEW.md.

Impact:
N/A
