# 1. Change:
- group vars and host vars
- inventory (production and staging)
- ansible.cfg
# 2. Then use:
## install docker on servers: docker.yml
```bash
ansible all -m ping --ask-vault-pass
ansible-galaxy install -f -r docker_requierements.yml
ansible-playbook docker.yml --ask-vault-pass
```
## install k8s on servers: k8s.yml
```bash
ansible all -m ping --ask-vault-pass
ansible-galaxy install -f -r k8s_requierements.yml
ansible-playbook k8s.yml --ask-vault-pass
```

## You can speed up your roles with:
### 1. Turn of host_key_checking for stable and secure environment:
```
[defaults]
host_key_checking = False
```
### 2. SSH multiplexing:
The first thing to check is whether the SSH connection re–use works. Since Ansible performs all actions via SSH, any delay in establishing a connection significantly slows down the execution of the playbook as a whole.
<br>In the ansible.cfg add:
```
[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s
```
Check: 
```bash
ansible staging_servers -vvvv -m ping
``` 
You should see: 
```
SSH: EXEC ssh -vvv -C -o ControlMaster=auto -o ControlPersist=60s... -o ControlPath=/home/ubuntu/.ansible/cp/7c223265ce
```
### 3. Enable Pipeling:
```
[ssh_connection]
pipelining = true
```
Check: 
```bash
ansible staging_servers -vvv -m ping
``` 
You should see ONE ssh call. Not a few.
### 4. Turn off gather_facts:
```bash
gather_facts: no
```
### 5. Place the ansible master in the environment closest to the target hosts. For example in the same AWS region.

### 6. Use Mitogen for ansible.
