# Backup (restic)

## features
daily backup, dump of the database and snapshots to restore from 


## Enable it
```ini
[backups]
debian
```
`group_vars/backups.yml`:
```yaml
backup_enabled: true
backup_postgresql_dump: true
backup_paths:
  - /etc/wireguard
  - /etc/nginx
  - /var/www/lab
```
Set `backup_postgresql_dump: false` if the host has no database.

## Settings
| Variable | Default | What it does |
|---|---|---|
| `backup_enabled` | `false` | Turns the role on |
| `backup_repo` | `/var/backups/restic` | Where the backups are stored |
| `backup_paths` | `/etc` | Folders to back up |
| `backup_keep_daily` | `7` | Daily snapshots to keep |
| `backup_keep_weekly` | `4` | Weekly snapshots to keep |
| `backup_hour`, `backup_minute` | `2`, `30` | Time of the daily run |
| `backup_postgresql_dump` | `false` | Dump the database first |

## Run it
```sh
ansible-playbook site.yml --limit SERVERNAME
```

## Check it works
```sh
ansible backups -b -m command -a "/usr/local/sbin/lab-restore-test"
```
You should see `restore test ok`. `scripts/verify` runs this too.

## Restore a file
```sh
RESTIC_REPOSITORY=/var/backups/restic RESTIC_PASSWORD_FILE=/root/.restic-password \
  restic restore latest --target / --include /var/www/lab/index.html
