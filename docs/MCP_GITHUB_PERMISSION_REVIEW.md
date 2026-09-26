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