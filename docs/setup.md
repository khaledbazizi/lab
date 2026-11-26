# Setup

Prepare your servers,setup the Services (web, VPN, database, backup).

## What you need

**Control machine** (where you run Ansible)
- Ansible, with the two collections the roles use:
  ```sh
  ansible-galaxy collection install community.general ansible.posix
  ```
- An SSH key pair. 

**Each server**
- An admin user that can log in with your SSH key
- Passwordless sudo for that user
- Python3

The playbook does not create the first admin user. Ansible has to log in before it can run, so this account must exist first. A VPS provider usually creates it when you add your SSH key.

## Prepare a server

Run this once on each new server, as root. Replace `youruser` with your username.

**Debian**
```sh
apt update && apt -y install sudo python3
adduser --disabled-password --gecos "" youruser
usermod -aG sudo youruser
echo "youruser ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/youruser
chmod 440 /etc/sudoers.d/youruser
```

**Alpine**
```sh
apk update && apk add python3 sudo
adduser youruser
adduser youruser wheel
echo '%wheel ALL=(ALL:ALL) NOPASSWD: ALL' > /etc/sudoers.d/wheel
chmod 440 /etc/sudoers.d/wheel
```
If `apk add sudo` fails, uncomment the `community` line in `/etc/apk/repositories` and run `apk update`.

**Then, from your machine, copy your key and test it**
```sh
ssh-copy-id youruser@SERVER_IP
ssh -o BatchMode=yes youruser@SERVER_IP 'echo key ok; sudo -n true && echo sudo ok'
```
Do not continue until you see `key ok` and `sudo ok`. The playbook turns off password login.

## Configure the inventory

```sh
cd ansible
cp inventory.example.ini inventory.ini
$EDITOR inventory.ini
```

Add your servers and your username:
```ini
[lab]
debian ansible_host=SERVER_IP
alpine ansible_host=SERVER_IP

[lab:vars]
ansible_user=youruser
```
Keep only the servers you have. The name at the start of each line becomes the hostname (`lab-<name>`). Your `inventory.ini` is ignored by Git, so it is never committed.

Check the connection:
```sh
ansible lab -m ping
```
Every server should answer `pong`.

## Run it

Open a second SSH session to each server first and leave it open. The SSH and firewall changes can lock you out if something is wrong, and an open session survives.

```sh
ansible-playbook site.yml --check --diff    # dry run: shows what would change
ansible-playbook site.yml                   # apply
ansible-playbook site.yml                   # run again: should report changed=0
```

On a brand-new server, the dry run can fail at the nftables step, because `--check` does not install packages. This is expected. Run the playbook for real. See [Troubleshooting](troubleshooting.md).

Useful options:
```sh
ansible-playbook site.yml --limit SERVERNAME             # one server only
ansible-playbook site.yml -e base_upgrade_packages=true  # also upgrade packages (off by default)
```

## What the baseline does

| Role | What it sets up |
|---|---|
| `base` | Refreshes package lists, installs common tools, sets the hostname and timezone |
| `users` | Creates the admin user, installs its SSH key, adds it to `sudo` (Debian) or `wheel` (Alpine) |
| `ssh_hardening` | No root login, no password login, 3 attempts per connection, no X11 forwarding |
| `firewall` | nftables, default deny: only SSH, ping and DHCP are allowed in |

The playbook stops at the start on any other OS, before changing anything.

## Check it worked

```sh
../scripts/verify
```

It prints PASS or FAIL for each check and exits 0 when everything passes.

Baseline checks:
- servers reachable
- root login disabled
- password login disabled
- keyboard-interactive login disabled
- X11 forwarding disabled
- max 3 auth tries
- firewall default deny on incoming traffic
- firewall enabled at boot
- no configuration drift (a dry run would change nothing)

Service checks (run only for hosts in that group):
- nginx running, and answering on port 80
- WireGuard listening, and the firewall allows its port
- PostgreSQL online, and closed outside the tunnel
- a backup snapshot exists, and the restore test passes
