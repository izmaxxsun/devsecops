# Ansible
Tool for automation

## Inventory
List of assets you want to do things to. Can be static or dynamically created thru code (useful for cloud).

## Running Commands
### Ad Hoc
```
# pings every server in your inventory
ansible all -m ping

# performs yum install on inventory group "webservers"
ansible webservers -m yum -a "name=httpd state=installed"
```

### Playbooks
Can be version controlled and reused

```
ansible-playbook <command>
```

