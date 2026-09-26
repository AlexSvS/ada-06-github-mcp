|           Tool name           |    Tool set   | Read/Write |                            Use                            |        Risk        |
|:-----------------------------:|:-------------:|:----------:|:---------------------------------------------------------:|:------------------:|
| get_commit                    | repos         | Read       | Retrieves information about a specific commit.            | Low                |
| get_file_contents             | repos         | Read       | Reads the contents of a file in a repository.             | Low                |
| get_label                     | issues        | Read       | Retrieves information about a specific issue label.       | Low                |
| issue_read                    | issues        | Read       | Reads details of a specific GitHub issue.                 | Low                |
| list_branches                 | repos         | Read       | Lists branches in a repository.                           | Low                |
| list_commits                  | repos         | Read       | Lists commits from a repository.                          | Low                |
| list_issue_fields             | issues        | Read       | Lists available fields/metadata associated with issues.   | Low                |
| list_issue_types              | issues        | Read       | Lists the issue types available in a repository.          | Low                |
| list_issues                   | issues        | Read       | Lists issues in a repository.                             | Low                |
| list_pull_requests            | pull_requests | Read       | Lists pull requests in a repository.                      | Low                |
| list_repository_collaborators | repos         | Read       | Lists users who have collaborator access to a repository. | Medium             |
| pull_request_read             | pull_requests | Read       | Reads details of a specific pull request.                 | Low                |
| search_code                   | repos         | Read       | Searches source code across repositories.                 | Medium             |
| search_commits                | repos         | Read       | Searches GitHub commits.                                  | Low                |
| search_issues                 | issues        | Read       | Searches issues across repositories.                      | Low                |
| search_pull_requests          | pull_requests | Read       | Searches pull requests.                                   | Low                |
| search_repositories           | repos         | Read       | Searches for GitHub repositories.                         | Low                |
| write tools                   | -             | N/A        | N/A in READ-ONLY                                          | High (if included) |