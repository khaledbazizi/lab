# Web server (nginx)

## features
nginx server site

## Enable it
Add the host to the `webservers` group in `inventory.ini`:
```ini
[webservers]
debian
```
Group variables go in `group_vars/webservers.yml`. It sets `nginx_enabled: true` and opens port 80 in the firewall.

## Settings
| Variable | Default | What it does |
|---|---|---|
| `nginx_enabled` | `false` | Turns the role on |
| `nginx_web_root` | `/var/www/lab` | Folder that holds the site files |
| `nginx_sites` | one default site | List of sites to serve |

## Run it
```sh
ansible-playbook site.yml --limit SERVERNAME
```

## Check it works
```sh
curl -s http://SERVER_IP
```
You should see the page. `scripts/verify` checks that nginx is running and answers on port 80.
