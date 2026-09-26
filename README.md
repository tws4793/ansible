# ansible

Role-based Ansible playbook for provisioning a workstation on macOS, Debian/Ubuntu and Fedora/RHEL. It runs against `localhost`.

To set up a new machine, including WSL, the main path is `setup.sh` in [tws4793/dotfiles](https://github.com/tws4793/dotfiles#readme). That script installs fnm and uv. This repo installs nvm and pyenv instead.

## Roles

| Role | What it does | Toggle (default) |
| --- | --- | --- |
| `base` | System upgrade and core CLI packages (Homebrew, apt or dnf) | always on |
| `dotfiles` | Clones the bare dotfiles repo into `~/.df` and checks it out | `install_dotfiles` (true) |
| `desktop` | GUI apps: Homebrew casks on macOS, Flatpaks + Chrome + VS Code on Linux | `install_desktop` (true) |
| `python` | pyenv and Python 3.13 | `install_python` (true) |
| `node` | nvm and Node.js 20.16 | `install_node` (true) |
| `ngrok` | ngrok | `install_ngrok` (true) |
| `forticlient` | FortiClient VPN / NetworkManager fortisslvpn | `install_forticlient` (false) |

Defaults live in [`inventory/group_vars/all.yml`](inventory/group_vars/all.yml).

The `bootstrap`, `nvm` and `server` roles and `dotfiles.yml` are older standalone pieces that `main.yml` doesn't use.

## Usage

1. Install Ansible:

   ```console
   brew install ansible            # macOS
   sudo apt install -y ansible     # Debian/Ubuntu
   sudo dnf install -y ansible     # Fedora
   ```

2. Clone the repo and install the required collections:

   ```console
   git clone https://github.com/tws4793/ansible.git && cd ansible
   ansible-galaxy collection install -r requirements.yml
   ```

3. Run the playbook. `-K` asks for your sudo password:

   ```console
   ansible-playbook main.yml -K
   ```

To run only some roles, or to override a toggle:

```console
ansible-playbook main.yml -K --tags base,python
ansible-playbook main.yml -K -e '{"install_ngrok": false}'
```

## Notes

- **Shell config:** with `manage_shell_rc: true` (the default), the `dotfiles`, `python` and `node` roles append lines to `~/.<shell>rc`. My dotfiles track `~/.zshrc`, so set it to `false` when using them.
- **WSL:** set `install_desktop: false`, since the Linux desktop role installs Flatpaks and GNOME apps.
- **Dotfiles clone:** the dotfiles role clones over SSH, so the machine needs a GitHub SSH key first.
