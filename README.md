# Requirements
## Windows 11
- WSL2
- Ubuntu 22.04
- VirtualBox
  VMs with Ubuntu 22.04
- VSCode

## WSL Ubuntu 22.04
- ansible
- sshpass

```
apt update && apt install -y ansible sshpass
```

# Environment
# ![diagram](./diagram/diagram.drawio.png)


# Configure Ansible server (WSL2)
## 1. Create 3 vms
- vms credentials
  - user: osboxes
  - password: osboxes.org

```
cd vms

./virtualbox_create_vm.sh docker01
./virtualbox_create_vm.sh docker02
./virtualbox_create_vm.sh docker03
```

## 2. Configure /etc/hosts file
```
./set_docker_hosts_on_ansible_server.sh         # set hosts on /etc/hosts server file
cat /etc/hosts                                  # check hosts on /etc/hosts file
```

## 3. Create ssh key on Docker machines
```
ssh-keygen -f ~/.ssh/key_docker_machines
```

## 4. Copy publick key Ansible server to Ansible hosts
```
ssh-copy-id osboxes@docker01
ssh-copy-id osboxes@docker02
ssh-copy-id osboxes@docker03

or

echo password > password

for user in osboxes
do
  for instance in docker01 docker02 docker03
  do
    sshpass -f password.txt ssh-copy-id -o StrictHostKeyChecking=no ${user}@${instance}
  done
done
```

## 5. Check connectiviy
```
ansible -i docker01,docker02,docker03 all -m ping --user osboxes
```

## 6. Configure Ansible inventory
```
cat > /etc/ansible/hosts <<EOF
[docker_machines]
docker01 ansible_host=docker01 ansible_user=osboxes
docker02 ansible_host=docker02 ansible_user=osboxes
docker03 ansible_host=docker03 ansible_user=osboxes

[all:vars]
ansible_python_interpreter=/usr/bin/python3
EOF
```

## 7. List inventory
```
ansible-inventory --list -y
```

## 8. Ping all hosts
```
ansible all -m ping
```

## 9. Run playbooks
1. [setup.yml](./playbooks/setup.yml)

```
cd playbooks
ansible-playbook setup.yml              # install and configure 3 vms
```

# Notes
```
```

# Links
