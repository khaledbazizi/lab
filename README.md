### Initial setup 

#### ansible roles for a secure baseline, using alpine and debian 

| Role | What it sets up |
|---|---|
| `base` | deal with packages, install needed tools and set hostname and timezone |
| `users` | set admin user and add ssh key  |
| `ssh_hardening` | no root,passowrd and limit 3 attempts per connections and disable X11 forwarding |
| `firewall` | nftables for only SSH, ping and DHCP |



#### TODO 
- add documentation for what's already done
- add tests  
