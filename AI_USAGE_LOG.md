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
Established a detailed, verified architectural baseline of the repository with zero remote mutations, clearly diagnosing the root cause of the version mismatch between PR #1 and Issue #2.

### Entry 03 — Title

Tool: 
Date: 
Stage: Issue / PR analysis

Prompt:


AI contribution:


Student decision:


Impact:

### Entry 04 — Title

Tool: 
Date: 
Stage: Permission/security review 

Prompt:


AI contribution:


Student decision:


Impact:
