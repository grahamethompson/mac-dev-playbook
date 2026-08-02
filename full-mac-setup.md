# Full Mac Setup Process

This is the manual checklist for bringing a new Mac up to the same local development baseline managed by this playbook.

## Initial macOS Setup

Before running the playbook:

  - Complete Apple's first-run setup wizard and create the local user account.
  - Install Apple's command line tools: `xcode-select --install`.
  - Install Homebrew if it is not already available.
  - Install Ansible with pipx:

    ```sh
    brew install pipx
    pipx install ansible
    pipx ensurepath
    ```

  - Clone this repository:

    ```sh
    git clone git@github.com:grahamethompson/mac-dev-playbook.git
    cd mac-dev-playbook
    ```

  - Install the required Ansible roles and collections:

    ```sh
    ansible-galaxy install -r requirements.yml
    ```

  - Run the playbook:

    ```sh
    ansible-playbook main.yml --ask-become-pass
    ```

## Managed Baseline

The playbook installs the current local development stack:

  - Homebrew taps: `ddev/ddev`, `dotenvx/brew`, `pygmystack/pygmy`, `upsun/tap`
  - Container tooling: Colima, Docker CLI, Docker Buildx, Docker Compose, and Pygmy
  - Local web tooling: DDEV, Ahoy, mkcert, ngrok, and Homebrew build/runtime dependencies used by the PHP and image-processing stack
  - Language tooling: asdf, Node.js 22.22.2, PHP 8.3.30, pnpm, yarn, Mermaid CLI, KaTeX, and Playwright
  - CLI tooling: git, GitHub CLI, GitLab CLI, act, Task, ripgrep, tree, dotenvx, Google Cloud CLI, upsun-cli, pandoc, pipx, and wget
  - Developer desktop apps: ChatGPT, Claude Code, Codex, Firefox, GitHub Desktop, iTerm2, Obsidian, PhpStorm, Sequel Ace, Sublime Text, Transmit, and VS Code; all managed casks are listed in `default.config.yml`

## After the Playbook

Some setup still needs a signed-in desktop session:

  - Start Colima if it is not running: `colima start`.
  - Confirm Docker is reachable: `docker version`.
  - Confirm DDEV can see Docker: `ddev version`.
  - Authenticate Google Cloud CLI and ngrok as needed. Keep their generated credentials in their user-level config directories, never in this repository.
  - Sign in to Dropbox and allow it to finish syncing.
  - Sign in to Bitwarden, GitHub Desktop, Slack, PhpStorm, Proton VPN, Transmit, and any other account-backed apps.
  - Open Obsidian and connect the local vault location.
  - Review macOS Privacy & Security prompts for Terminal, iTerm2, Dropbox, developer tools, and VPN/network extensions.
  - Install App Store apps manually if needed; `mas` is not part of the current managed baseline.

## Manual Preferences

These are intentionally left out of the default run because they are personal, interactive, or easy to review manually:

  - System Settings > Keyboard: set Caps Lock to Escape, key repeat to Fast, delay until repeat to Short.
  - System Settings > Privacy & Security: grant Full Disk Access to Terminal or iTerm2 when needed.
  - Finder: review sidebar favorites and Desktop behavior.
  - Safari: enable the Develop menu and show the full website address if Safari is used for testing.
  - Dock: arrange pinned apps after the core apps are installed.

## Before Formatting an Old Mac

  - Confirm any unsynced development work is pushed or copied.
  - Confirm Dropbox, Obsidian vaults, SSH config, app settings, and local databases are backed up.
  - Sign out of licensed apps and services that limit device activations.
  - Deauthorize Apple media accounts if needed.
  - Follow Apple's erase or trade-in guidance for the macOS version on that machine.
