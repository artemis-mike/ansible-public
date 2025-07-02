# Synopsis
A Ansible repository that installs various software especially via docker containers.

# Structure
```
ansible/
├─ roles/
│  ├─ example/
│  │  ├─ files/
│  │  │  ├─ docker-compose.yaml
│  │  │  ├─ .env
│  │  │  ├─ .env.example
│  │  ├─ handlers/
│  │  ├─ tasks/
│  │  │  ├─ main.yaml
│  │  ├─ .../
│  │  ├─ README.md
│  │  ├─ setup.yaml
├─ group_vars/
├─ host_vars/
├─ inventory.yaml
```

# Usage
To create a new role execute
```
ansible-galaxy init <service_name>
```
Create a playbook `setup.yaml`

```
- name: Setup <service_name> docker containers
  hosts: all
  gather_facts: yes
  become: yes

  roles:  # Among other, automatically run tasks/main and handlers/main of these roles.
    - <service_name>  # The primary role / service_name role you want to setup.
    - docker-common   # Common tasks to set up the service
    - check-traefik   # Role to handle traefik if needed by the service's docker-compose.yaml file.

  # You can add additional tasks that will be executed after the roles above like
  tasks: 
    - import_tasks: tasks/<task1>.yaml  # import <task1>.yaml from this roles tasks
```

Also create the `task/main.yaml` file of the service role with exactly this content. This will be used by the `docker-common` role.
```
- set_fact:
    service_name: "{{ role_name }}"
    service_files_path_prefix: "{{ role_path }}"
```

To set up a service, run the corresponding playbook
```
ansible-play -i inventory.yaml -l <hostname> roles/<service_name>/setup.yaml
```

# Deep-Dive
See 
- `roles/docker-common/tasks/main.yaml`
- `roles/docker-common/handlers/main.yaml`

files to understand, what the role `docker-common` does to set up a service.