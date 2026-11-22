# Setup

Prepare servers and run the playbook.

## What you need

### Control machine
- Ansible with `community.general` and `ansible.posix` collections
- SSH key pair

### Each server
- Admin user with key access
- User with root access
- `python3`

## Prepare server

- Initial config on server
- Edit `$USER`

**Debian**
```sh
apt update && apt -y install sudo python3
adduser --disabled-password --gecos "" $USER
usermod -aG sudo $USER
echo "$USER ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$USER
chmod 440 /etc/sudoers.d/$USER
```

**Alpine**
```sh
apk update && apk add python3 sudo
adduser $USER
adduser $USER wheel
echo '%wheel ALL=(ALL:ALL) NOPASSWD: ALL' > /etc/sudoers.d/wheel
chmod 440 /etc/sudoers.d/wheel
```
- For the sudo package, enable the `community` repo in `/etc/apk/repositories` and run `apk update`

## Configure the inventory

```sh
cd ansible
cp inventory.example.ini inventory.ini
$EDITOR inventory.ini
```

- Add your Debian or Alpine servers list:

```ini
[lab]
debian ansible_host=
alpine ansible_host=

[lab:vars]
ansible_user=$USER
```

- Do a check:

```sh
ansible lab -m ping
```

## Run it

```sh
ansible-playbook site.yml --check --diff    # dry run
ansible-playbook site.yml                   # apply
ansible-playbook site.yml                   # run again, should be changed=0
```

## Verify script

```sh
../scripts/verify
```

Tests:
- Servers reachable
- Root login disabled
- Password login disabled
- Keyboard-interactive login disabled
- X11 forwarding disabled
- Max 3 auth tries
- Firewall default deny on incoming traffic
- Firewall enabled at boot
- No configuration drift
