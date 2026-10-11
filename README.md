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

## Manual setup (without Ansible)

On an amd64 Debian/Ubuntu or Linux Mint host:

```bash
. /etc/os-release
ubuntu_codename="${UBUNTU_CODENAME:-$VERSION_CODENAME}"
wine_repo_url=https://dl.winehq.org/wine-builds/ubuntu
if [[ "$ID" == debian ]]; then
  wine_repo_url=https://dl.winehq.org/wine-builds/debian
fi
wine_architectures=amd64,i386
if [[ "$ubuntu_codename" == resolute ]]; then
  wine_architectures=amd64
fi
sudo dpkg --add-architecture i386
sudo apt install curl gnupg
sudo install -d -m 0755 /etc/apt/keyrings
curl -fL https://dl.winehq.org/wine-builds/winehq.key -o /tmp/winehq.key
sudo gpg --dearmor --yes --output /etc/apt/keyrings/winehq-archive.gpg /tmp/winehq.key
sudo chmod 0644 /etc/apt/keyrings/winehq-archive.gpg
rm /tmp/winehq.key
echo "deb [arch=$wine_architectures signed-by=/etc/apt/keyrings/winehq-archive.gpg] $wine_repo_url/ $ubuntu_codename main" | sudo tee /etc/apt/sources.list.d/winehq.list
sudo apt update
```

The role also applies the following Ubuntu archive pin and aligns `libgd3`
architectures before installation. This workaround is Ubuntu/Mint-specific;
do not apply the Ubuntu origin pin on Debian. Review any proposed downgrades:

```bash
printf 'Package: libgd3\nPin: release o=Ubuntu\nPin-Priority: 1001\n' | sudo tee /etc/apt/preferences.d/wine-libgd3-pin
sudo apt install --allow-downgrades libgd3 libgd3:i386
```

Install Wine with recommended dependencies:

```bash
sudo apt install --install-recommends winehq-stable
```

Wine Mono and GPU-specific 32-bit drivers are optional manual additions,
not installed by the role. Wine Mono installers are available at
https://dl.winehq.org/wine/wine-mono/.

## License

MIT

## Author

Varssos