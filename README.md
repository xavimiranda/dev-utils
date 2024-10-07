# Utils
This repository contains a diverse collection of utilities designed to be useful across various development environments, programming languages, and workflows. These tools aim to streamline processes and enhance productivity, making them adaptable to different project needs and team setups.

## Git Hooks 

### Overview
This repository contains reusable Git hooks that can be linked to multiple Git repositories. It includes:
- A **pre-commit "barrel" hook** that serves as a central dispatcher for running multiple pre-commit scripts.
- Additional **custom hook scripts** (e.g., `pre-commit.appsettings`), each of which can perform specific checks.

### Features
- **Modular Pre-commit Hooks**: The pre-commit "barrel" script runs all `pre-commit.*` scripts in the common Git directory.
- **Worktree Support**: Ensures compatibility with Git repositories that use worktrees by utilizing `git rev-parse --git-common-dir`.
- **Hook Filtering**: Automatically skips any hook scripts that end with `sample`, preventing example hooks from running.

### Repository Structure

### Installation

1. **Clone this repository** to a location where you want to store the shared hooks:

2. **Link the hooks to your target repositories**. Inside the target Git repository, link the `pre-commit` hook:
    ```bash
    ln -s /path/to/shared-git-hooks/pre-commit .git/hooks/pre-commit
    ```

3. **Link or add new additional pre-commit scripts** in the shared repository as needed. These scripts should follow the naming convention `pre-commit.*` (e.g., `pre-commit.lint.sh`) and be executable.

4. **Ensure scripts are executable**:
    ```bash
    chmod +x /path/to/shared-git-hooks/pre-commit
    chmod +x /path/to/shared-git-hooks/pre-commit.*
    ```
> Note that symbolic linking doesn't work on windows without admin priveleges. Hard linking does work so use `ln /path/to/shared-git-hooks/pre-commit .git/hooks/pre-commit` instead

### Example Hook: `pre-commit.appsettings.sh`

The `pre-commit.appsettings.sh` script checks for any staged `appsettings` files, warns the user, and prompts them for confirmation before proceeding with the commit.

```bash
#!/bin/bash
if git diff --cached --name-only | grep -q 'appsettings'; then
    echo "Warning: appsettings file(s) are staged for commit."
    read -p "Are you sure you want to continue? (y/n): " confirm < /dev/tty
    if [[ "$confirm" != "y" ]]; then
        exit 1
    fi
fi
