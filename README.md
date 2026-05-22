<img src="files/Mac-Dev-Playbook-Logo.png" width="250" height="156" alt="Mac Dev Playbook Logo" />

# Grahame's Mac Development Ansible Playbook

[![CI][badge-gh-actions]][link-gh-actions]

This playbook installs and configures the software I use on my Mac for local web and software development. The default configuration in `default.config.yml` reflects the current local environment in this checkout: Homebrew-managed developer tools, Colima/Docker CLI, DDEV, asdf-managed Node.js and PHP, and the desktop apps I use day to day.

## Installation

  1. Ensure Apple's command line tools are installed (`xcode-select --install` to launch the installer).
  2. [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html). This Mac currently uses `pipx`:

     1. Install `pipx` if needed: `brew install pipx`
     2. Install Ansible: `pipx install ansible`
     3. Ensure the `pipx` binary directory is on your path: `pipx ensurepath`

  3. Clone or download this repository to your local drive.
  4. Run `ansible-galaxy install -r requirements.yml` inside this directory to install required Ansible roles.
  5. Run `ansible-playbook main.yml --ask-become-pass` inside this directory. Enter your macOS account password when prompted for the 'BECOME' password.

> Note: If some Homebrew commands fail, you might need to agree to Xcode's license or fix some other Brew issue. Run `brew doctor` to see if this is the case.

### Use with a remote Mac

You can use this playbook to manage other Macs as well; the playbook doesn't even need to be run from a Mac at all! If you want to manage a remote Mac, either another Mac on your network, or a hosted Mac like the ones from [MacStadium](https://www.macstadium.com), you just need to make sure you can connect to it with SSH:

  1. (On the Mac you want to connect to:) Go to System Settings > Sharing.
  2. Enable 'Remote Login'.

> You can also enable remote login on the command line:
>
>     sudo systemsetup -setremotelogin on

Then edit the `inventory` file in this repository and change the line that starts with `127.0.0.1` to:

```
[ip address or hostname of mac]  ansible_user=[mac ssh username]
```

If you need to supply an SSH password (if you don't use SSH keys), make sure to pass the `--ask-pass` parameter to the `ansible-playbook` command.

### Running a specific set of tagged tasks

You can filter which part of the provisioning process to run by specifying a set of tags using `ansible-playbook`'s `--tags` flag. The tags available are `dotfiles`, `homebrew`, `mas`, `dock`, `sudoers`, `terminal`, `extra-packages`, `sublime-text`, `osx`, and `post`.

    ansible-playbook main.yml -K --tags "dotfiles,homebrew"

## Overriding Defaults

Not everyone's development environment and preferred software configuration is the same.

You can override any of the defaults configured in `default.config.yml` by creating a `config.yml` file and setting the overrides in that file. For example, you can customize the installed packages and apps with something like:

```yaml
homebrew_installed_packages:
  - git
  - go

mas_installed_apps:
  - { id: 443987910, name: "1Password" }
  - { id: 498486288, name: "Quick Resizer" }
  - { id: 557168941, name: "Tweetbot" }
  - { id: 497799835, name: "Xcode" }

composer_packages:
  - name: hirak/prestissimo
  - name: drush/drush
    version: '^8.1'

gem_packages:
  - name: bundler
    state: latest

npm_packages:
  - name: webpack

pip_packages:
  - name: mkdocs

configure_dock: true
dockitems_remove:
  - Launchpad
  - TV
dockitems_persist:
  - name: "Sublime Text"
    path: "/Applications/Sublime Text.app/"
    pos: 5
```

Any variable can be overridden in `config.yml`; see the supporting roles' documentation for a complete list of available variables.

## Included Applications / Configuration (Default)

Applications (installed with Homebrew Cask):

  - [BasicTeX](https://tug.org/mactex/morepackages.html)
  - [Bitwarden](https://bitwarden.com/)
  - [BlackHole 2ch](https://existential.audio/blackhole/)
  - [ChatGPT](https://openai.com/chatgpt/download/)
  - [ChromeDriver](https://sites.google.com/chromium.org/driver/)
  - [Claude Code](https://www.anthropic.com/claude-code)
  - [Codex CLI](https://github.com/openai/codex)
  - [Codex app](https://openai.com/codex/)
  - [Copilot CLI](https://github.com/github/copilot-cli)
  - [Dropbox](https://www.dropbox.com/)
  - [Firefox](https://www.mozilla.org/en-US/firefox/new/)
  - [Handbrake](https://handbrake.fr/)
  - [GitHub Desktop](https://desktop.github.com/)
  - [iTerm2](https://iterm2.com/)
  - [LibreOffice](https://www.libreoffice.org/)
  - [LICEcap](http://www.cockos.com/licecap/)
  - [Obsidian](https://obsidian.md/)
  - [Open WebUI](https://openwebui.com/)
  - [PhpStorm](https://www.jetbrains.com/phpstorm/)
  - [Proton VPN](https://protonvpn.com/)
  - [Sequel Ace](https://sequel-ace.com) (MySQL client)
  - [Slack](https://slack.com/)
  - [SourceTree](https://www.sourcetreeapp.com/)
  - [Spotify](https://www.spotify.com/)
  - [Stats](https://github.com/exelban/stats)
  - [Sublime Text](https://www.sublimetext.com/)
  - [Transmit](https://panic.com/transmit/) (S/FTP client)
  - [Visual Studio Code](https://code.visualstudio.com/)

Packages (installed with Homebrew):

  - act
  - asdf
  - autoconf
  - bison
  - colima
  - coreutils
  - curl
  - ddev
  - docker
  - docker-buildx
  - dotenvx
  - freetype
  - gd
  - gettext
  - gh
  - git
  - go
  - icu4c@78
  - jpeg
  - krb5
  - libedit
  - libiconv
  - libpng
  - libsodium
  - libtiff
  - libxml2
  - libzip
  - mkcert
  - oniguruma
  - openssl@3
  - pandoc
  - pcre2
  - pipx
  - pnpm
  - pkgconf
  - re2c
  - upsun-cli
  - wget
  - yarn

Language runtimes:

  - Node.js 22.22.2 via asdf
  - PHP 8.3.30 via asdf

Homebrew taps:

  - ddev/ddev
  - dotenvx/brew
  - upsun/tap

Dotfiles, macOS defaults, Terminal profile, Dock management, and Sublime Text settings are supported by the playbook but disabled by default for this local environment. Enable them with the corresponding `configure_*` variables in `config.yml`.

Finally, the post-provision step installs the configured asdf plugins and sets the default Node.js and PHP versions.

## Full / From-scratch setup guide

The from-scratch setup guide captures the remaining manual steps for a new Mac after the automated playbook run.

You can see my full from-scratch setup document here: [full-mac-setup.md](full-mac-setup.md).

## Testing the Playbook

This project can be tested on GitHub Actions' macOS infrastructure.

You can also run macOS itself inside a VM, for at least some of the required testing (App Store apps and some proprietary software might not install properly). I currently recommend:

  - [UTM](https://mac.getutm.app)
  - [Tart](https://github.com/cirruslabs/tart)

## Ansible for DevOps

Check out [Ansible for DevOps](https://www.ansiblefordevops.com/), which teaches you how to automate almost anything with Ansible.

## Credits

This playbook is based on [Jeff Geerling's Mac Dev Playbook](https://github.com/geerlingguy/mac-dev-playbook), originally inspired by [MWGriffin/ansible-playbooks](https://github.com/MWGriffin/ansible-playbooks).

[badge-gh-actions]: https://github.com/grahamethompson/mac-dev-playbook/actions/workflows/ci.yml/badge.svg
[link-gh-actions]: https://github.com/grahamethompson/mac-dev-playbook/actions/workflows/ci.yml
