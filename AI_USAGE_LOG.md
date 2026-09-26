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


### Entry 02 — Title

Tool: Antigravity (Gemini 3.8 Flash)
Date: 2026-09-26
Stage: Repository analysis

Prompt:


AI contribution:


Student decision:


Impact:

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
