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
- `src/customer_search/__main__.py`: Execution entry point for CLI module execution (`python -m customer_search`).
- `src/customer_search/cli.py`: Presentation layer handling CLI argument parsing, console output, exit code management.
- `src/customer_search/service.py`: Business logic layer executing case-insensitive substring search across name and email, and input validation.
- `src/customer_search/storage.py`: Persistence layer handling JSON file reading, writing, and integrity checks.
- `src/customer_search/models.py`: Domain entity definition (`Customer` dataclass).
- `customers.json`: Default local dataset file.
- `pyproject.toml`: Project build configuration and package metadata.
- `REQUIREMENTS.md`, `SPEC.md`, `ARQUITECTURE.md`, `TASKS.md`, `docs/traceability.md`: Specification and traceability documentation.

Tests:
- `tests/test_setup.py`: Verifies Python runtime (>= 3.11), package version and importability (`test_package_import`), dependency cleanliness, and local dataset structure.
- `tests/test_service.py`: Unit tests for `CustomerService` covering name/email partial matching, case-insensitivity, OR logic, and validation rules using a mock storage (`FakeStorage`).
- `tests/test_storage.py`: Persistence tests for `CustomerStorage` covering JSON reading, file creation, empty/malformed files, schema errors, and Unicode preservation using `tmp_path`.
- `tests/test_cli.py`: CLI integration and subprocess tests verifying exit codes (0, 1, 2), stderr error messages, 100-record search latency benchmark (<1.0s), and offline execution enforcement.
- `tests/conftest.py`: Shared pytest fixtures (`sample_customers_data`, `hundred_customers_data`, `hundred_customers_file`, `block_network`).


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
- Modifying REQUIREMENTS.md without updating SPEC.md, TASKS.md, or docs/traceability.md breaks specification integrity and traceability.
- Introducing FR-08 (deterministic ordering) without implementation conflicts with existing behavior that preserves file encounter order.
- Merging PR #1 as-is will break master's test suite due to the failing version assertion in test_package_import().

Questions:
- What is the intended ordering rule for FR-08 (e.g., ascending by ID, alphabetical by name)?
- What is the definition of "invalid characters or an invalid input format" for FR-09?
- Was the version assertion bump to 0.2.0 in tests/test_setup.py intentional, and if so, why were src/customer_search/__init__.py and pyproject.toml not updated?

Potential defects:
- test_package_import() fails in tests/test_setup.py because it asserts customer_search.__version__ == "0.2.0" while src/customer_search/__init__.py defines "0.1.0".
- Unannounced scope in PR #1: Commit f4b58c4 alters test_setup.py despite PR title and description indicating only requirements additions.

## 6. Traceability
Issue → Requirement/Spec → PR → Code → Test
|                            Issue/Need                            |   PR  |       Code / Files      |    Test / Evidence    | Estado |
|:----------------------------------------------------------------:|:-----:|:-----------------------:|:---------------------:|:------:|
| #2/Fix incorrect package version expected by test_package_import | PR #1 | src/tests/test_setup.py | test_package_import() | Gap    |
## 7. Permission Review
Authentication: GitHub Fine-Grained Personal Access Token (Bearer token configured in global `~/.gemini/config/mcp_config.json`)
GitHub token permissions: Repository Contents (Read), Issues (Read), and Pull requests (Read) restricted to selected repository (`AlexSvS/ada-05-spec-driven-feature`)
MCP read-only: Enforced via `"X-MCP-Readonly": "true"` header
Enabled toolsets: `"X-MCP-Toolsets": "repos,issues,pull_requests"`
Write capabilities exposed: None (0 write tools exposed; write/mutation tools were completely omitted from the tool schema during server negotiation)

## 8. Security Notes
- Credential exposure: Stored the PAT in the global user profile (`~/.gemini/config/mcp_config.json`) instead of inside the project repository to prevent committing secrets to version control.
- Prompt injection: Agent instructions strictly bounded to read-only queries with explicit repository scoping, preventing unauthorized remote exploration.
- Excess permissions: Followed principle of least privilege using read-only scopes. Write methods are unavailable at both the token and MCP header levels.
- Repository scope: Fine-grained token restricted strictly to target repositories rather than all account resources; queries restricted to `AlexSvS/ada-05-spec-driven-feature`.

## 9. Human Review
What did you verify yourself?
I manually inspected the codebase in `AlexSvS/ada-05-spec-driven-feature` to verify the 3-tier architecture, test files, and package version (`0.1.0`).. I also erified commit history on `update-branch` (commits `66ed604` and `f4b58c4`) to establish causality between the version assertion edit and Issue #2. And lastly, I verified MCP server configuration (`~/.gemini/config/mcp_config.json`) and inspected exposed tool schemas to ensure zero write tools were registered.

What AI conclusions did you reject or modify?
I rejected certain tools described in the agent since it invented some of them during the run. I modify some entries in the AI-USAGE-LOG.md to be more undertandable and natural and put N/A on the Impact section since nothing in the reviewed repository was changed.

## 10. Conclusion
What did MCP add to the engineering workflow?
- Real-time contextual introspection: Provided direct access to repository source code, issues, PR diffs, and commit history directly inside the development workflow without context switching to a browser.
- Secure read-only auditing: Enabled thorough architectural analysis and pre-merge PR inspection while enforcing strict read-only constraints, eliminating the risk of accidental mutations.
- Enhanced triage efficiency: Enabled fast root-cause identification for Issue #2 and rapid defect discovery in PR #1 with full traceability across requirements, code, and tests.
