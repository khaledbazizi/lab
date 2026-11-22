### Setup 

prepare servers and run the playbook 

#### what you need

#### Controle machine 
- Ansible wiht community.general and ansible.posix collections
- SSH key pair

#### Each Server:
- admin user with key acess
- user with root access
- python3

#### Prepare server

- intial config on server
- edit $USER

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
- for sudo package enable `community` repo in `/etc/apk/repositories` and do `apk update` 


#### Configure the inventory

```sh
cd ansible
cp inventory.example.ini inventory.ini
$EDITOR inventory.ini
```
- add your debian or alpine servers list  
[lab]
debian ansible_host=
alpine ansible_host=
[lab:vars]
ansible_user=$USER

- do a check:
```sh
ansible lab -m ping
```

#### Run it



```sh
ansible-playbook site.yml --check --diff    # dry run
ansible-playbook site.yml                   # apply
ansible-playbook site.yml                   # run again, should be changed=0
```


#### verify script

```sh
../scripts/verify
```
Tests: 
- servers reachable
- root login disabled
- password login disabled
- keyboard-interactive login disabled
- X11 forwarding disabled
- max 3 auth tries
- firewall default deny on incoming traffic
- firewall enabled at boot
- no configuration drift 

