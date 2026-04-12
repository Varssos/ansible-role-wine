# wine

Ansible role to install [Wine](https://www.winehq.org/) (winehq-stable) from the official WineHQ repository on Debian/Ubuntu systems.

## Requirements

- Debian or Ubuntu host
- `become: true` privileges (sudo)
- `ubuntu_codename` fact must be set (provided by `ubuntu-codename` role in pre_tasks)

## Role Variables

| Variable | Default | Description |
|---|---|---|
| `wine_package_name` | `winehq-stable` | Package name to install via apt |
| `wine_gpg_key_url` | `https://dl.winehq.org/wine-builds/winehq.key` | URL to the WineHQ GPG key |
| `wine_gpg_keyring` | `/etc/apt/keyrings/winehq-archive.key` | Path for the keyring file |

## Example Playbook

```yaml
- hosts: all
  become: true
  pre_tasks:
    - import_role:
        name: ubuntu-codename
  roles:
    - role: wine
```

## License

MIT

## Author

Varssos