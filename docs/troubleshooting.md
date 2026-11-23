## Find out what is wrong

**1. Can Ansible reach the servers?**
```sh
ansible lab -m ping
```

**2. What would the playbook change?**
```sh
ansible-playbook site.yml --check --diff
```

> **Note:** a dry run on a new server will show errors, because `--check` does not install the needed packages.
