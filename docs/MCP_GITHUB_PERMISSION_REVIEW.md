|              Use              | Needed | Granted |                                 Control                                | Decision |
|:-----------------------------:|:------:|:-------:|:----------------------------------------------------------------------:|:--------:|
| Read commit details           | Yes    | Yes     | PAT Contents: Read; MCP read-only; Fine-grained PAT selected repo      | Keep     |
| Read repository files/code    | Yes    | Yes     | PAT Contents: Read; MCP read-only; Fine-grained PAT selected repo      | Keep     |
| Read latest release           | No     | Yes     | PAT Contents: Read; MCP read-only                                      | Block    |
| Read release by tag           | No     | Yes     | PAT Contents: Read; MCP read-only                                      | Block    |
| Read Git tags                 | No     | Yes     | PAT Contents: Read; MCP read-only                                      | Block    |
| Read issue details            | Yes    | Yes     | PAT Issues: Read; MCP read-only; Fine-grained PAT selected repo        | Keep     |
| List repository branches      | Yes    | Yes     | PAT Contents: Read; MCP read-only                                      | Keep     |
| List commits                  | Yes    | Yes     | PAT Contents: Read; MCP read-only                                      | Keep     |
| Read issue fields             | Yes    | Yes     | PAT Issues: Read; MCP read-only                                        | Keep     |
| Read issue types              | Yes    | Yes     | PAT Issues: Read; MCP read-only                                        | Keep     |
| List issues                   | Yes    | Yes     | PAT Issues: Read; MCP read-only; Fine-grained PAT selected repo        | Keep     |
| List pull requests            | Yes    | Yes     | PAT Pull requests: Read; MCP read-only; Fine-grained PAT selected repo | Keep     |
| List releases                 | No     | Yes     | PAT Contents: Read; MCP read-only                                      | Block    |
| List repository collaborators | No     | Yes     | PAT Metadata/Administration access; MCP read-only                      | Block    |
| List Git tags                 | Yes    | Yes     | PAT Contents: Read; MCP read-only                                      | Keep     |
| Read pull request details     | Yes    | Yes     | PAT Pull requests: Read; MCP read-only; Fine-grained PAT selected repo | Keep     |
| Search repository code        | Yes    | Yes     | PAT Contents: Read; MCP read-only; Fine-grained PAT selected repo      | Keep     |
| Search commits                | Yes    | Yes     | PAT Contents: Read; MCP read-only                                      | Keep     |
| Search issues                 | Yes    | Yes     | PAT Issues: Read; MCP read-only                                        | Keep     |
| Search pull requests          | Yes    | Yes     | PAT Pull requests: Read; MCP read-only                                 | Keep     |
| Search repositories           | No     | Yes     | PAT Metadata: Read; MCP read-only                                      | Block    |

## Risk Analysis

| Risk | Example | Mitigation |
|---|---|---|
| **Accidental or Unauthorized Remote Modifications** | An automated agent unintentionally creates issues, posts comments on PRs, pushes commits, or merges code during an analysis session. | Enforce `"X-MCP-Readonly": "true"` in MCP configuration headers and provision read-only PATs to remove write capabilities at the protocol level. |
| **Credential / Token Exposure (Secret Leakage)** | A GitHub PAT is saved in a repository-level configuration file and inadvertently committed and pushed to remote version control. | Store MCP server credentials in the global user profile (`~/.gemini/config/mcp_config.json`) outside repository git tracking, and configure secret scanning. |
| **Excessive Permissions / Large Blast Radius** | A classic PAT with broad administrative and write permissions across all personal and organization repositories is shared with the tool. | Use GitHub Fine-Grained Personal Access Tokens scoped exclusively to specific repositories with least-privilege permissions and short expiration lifetimes. |
| **Unintended Scope Access / Data Exfiltration** | An agent executes global repository searches or reads sensitive metadata and code across organizational repositories outside the intended task. | Scope the Fine-Grained PAT to "Only select repositories", restrict toolsets via `X-MCP-Toolsets`, and explicitly instruct agents to bound query scope. |