# GitHub MCP Engineering Review — ADA-06
## 1. Repository
Owner / Repo: AlexSvS/ada-05-spec-driven-feature
Visibility: Public
Branch / reference analyzed: master and update-branch
## 2. MCP Connection
Server: https://api.githubcopilot.com/mcp/
Mode: READ-ONLY
Toolsets:
- repos
    - get_file_contents: Inspected directory structures (/, src/, tests/, docs/, results/) and read contents of source files, test files, configs, and documentation.
    - list_branches: Enumerated repository branches (master, update-branch).
    - list_commits: Inspected recent commit history on the repository.
- issues
    - list_issues: Retrieved open issues in AlexSvS/ada-05-spec-driven-feature (discovered Issue #2).
    - issue_read: Fetched issue details, status, and metadata for Issue #2.
- pull_requests
    - list_pull_requests: Discovered open pull requests in AlexSvS/ada-05-spec-driven-feature (discovered PR #1).
    - pull_request_read: Examined the unified diff (get_diff) and commit history (get_commits) for PR #1.
## 3. Repository Understanding
Purpose: The repository implements a Customer Search CLI tool built in Python 3.11+. Its core purpose is to allow offline searching of customer records (by name or email address using case-insensitive substring matching) stored in a local JSON dataset (customers.json).

Architecture summary:
The repository adheres to a classic 3-Tier Layered Architecture:
- Presentation Layer (src/customer_search/cli.py):
    - Parses CLI inputs via standard library argparse supporting positional queries ("smith"), query flags (-q, --query), and custom data paths (-f, --file).
    - Formats customer records (ID: <id> | Name: <name> | Email: <email>) or emits notification message (No customers found matching '<term>').
    - Controls process exit codes:
        - 0: Success (results found or no-match message displayed).
        - 1: Invalid arguments or query validation error (ValidationError).
        - 2: Storage or file access error (StorageError).
- Business Logic Layer (src/customer_search/service.py):
    - CustomerService: Receives customer data from storage conforming to StorageProtocol.
    - Validates query strings (raises ValidationError if empty, None, or whitespace-only).
    - Executes case-insensitive substring search matching across name OR email.
    - Preserves dataset encounter order.
- Data Persistence Layer (src/customer_search/storage.py):
    - CustomerStorage: Reads and writes local JSON files (customers.json).
    - Validates file existence, JSON syntax, array formatting, and record field types (id: int, name: str, email: str).
    - Encapsulates low-level file and JSON parsing exceptions in a domain-specific StorageError.
- Domain Model (src/customer_search/models.py):
    - Customer: An immutable @dataclass(frozen=True) holding id, name, and email.

Important files:


Tests:


## 4. Issue Analysis
Issue: 2 - Fix incorrect package version expected by test_package_import
Facts from Issue:
- In tests/test_setup.py, the test function test_package_import() verifies that customer_search.__version__ matches an expected string literal.
- A version conflict exists between the assertion in test_setup.py and the runtime value defined in src/customer_search/__init__.py.
- Git history reveals that on branch update-branch (associated with PR #1), commit f4b58c4cbefa4032f753f6c07a7bc8f616e2e3fc ("Introduce version validation test change") modified tests/test_setup.py line 18 from == "0.1.0" to == "0.2.0", but left src/customer_search/__init__.py and pyproject.toml unchanged at 0.1.0.
- The issue states that test_package_import() should not fail, and that the discrepancy between the expected 0.2.0 and actual 0.1.0 must be resolved.

Ambiguities:
- Authoritative Source of Truth: The issue title says "Fix incorrect package version expected by test_package_import", which hints that the expectation in the test is what's incorrect, but it does not explicitly state whether the package itself is intended to remain at 0.1.0 or be upgraded to 0.2.0.
- Target Branch Context: The issue does not specify whether this applies to master (where test_setup.py currently asserts 0.1.0 and passes) or to update-branch (PR #1, where the assertion was changed to 0.2.0 and fails).
- Synchronization with PR #1: PR #1 adds new requirements (FR-07, FR-08, FR-09). The issue does not clarify whether the version bump to 0.2.0 was intended to ship with those features or if it was an accidental edit.

Related requirements/spec:
- REQUIREMENTS.md
    - NFR-02: Requires test suite and CLI execution to maintain standard exit codes and reliability.
    - C-02: "Must be implemented using Python 3.11+ and pytest."
- SPEC.md
    - AC-09: "Search executes completely offline without external network or API calls, compatible with Python 3.11+ and pytest."

Related code:
- tests/test_setup.py: Contains test_package_import() asserting package version.
- src/customer_search/__init__.py: Contains runtime version (__version__)
- pyproject.toml: Contains metadata


Related tests:
test_package_import()

Risk or Edge Cases:
- Metadata Desynchronization: If src/customer_search/__init__.py is changed to 0.2.0 but pyproject.toml is left at 0.1.0, packaging tools (e.g., pip install -e ., wheel builds) will report mismatched versions.
- Premature Version Bumping: Bumping to 0.2.0 before PR #1's functional requirements (FR-07, FR-08, FR-09) are implemented releases a new minor version without the corresponding feature set.
- Accidental Release Masking: Reverting the test assertion to 0.1.0 without confirming team intent might accidentally revert an intended release version bump for update-branch.

## 5. Pull Request Review
PR: 1 - New requirements added
Changed behavior: Zero lines modified. No application behavior changed.
Changed files:
- REQUIREMENTS.md: Three new requirements were added to REQUIREMENTS.md (FR-07, FR-08, and FR-09)
- tests/test_setup.py: test_package_import() now asserts that customer_search.__version__ == "0.2.0". Because the source code (src/customer_search/__init__.py) still defines __version__ = "0.1.0", the test suite now fails.

Tests:
- test_package_import()
- No tests were added for the new requirements

Observations:
- The test modification in commit 2 directly caused the creation of Issue #2 ("Fix incorrect package version expected by test_package_import").
- No files implement the new functional requirements

Risks:

Questions:

Potential defects:

## 6. Traceability
Issue → Requirement/Spec → PR → Code → Test
|                            Issue/Need                            |   PR  |       Code / Files      |    Test / Evidence    | Estado |
|:----------------------------------------------------------------:|:-----:|:-----------------------:|:---------------------:|:------:|
| #2/Fix incorrect package version expected by test_package_import | PR #1 | src/tests/test_setup.py | test_package_import() | Gap    |
## 7. Permission Review
Authentication:
GitHub token permissions:
MCP read-only:
Enabled toolsets:
Write capabilities exposed:
## 8. Security Notes
- Credential exposure
- Prompt injection
- Excess permissions
- Repository scope
## 9. Human Review
What did you verify yourself?
What AI conclusions did you reject or modify?
## 10. Conclusion
What did MCP add to the engineering workflow?