# docker-common
## Synopsis
A role for deploying service via docker-compose-files.

## Usage
### Calling Playbook
Create playbook that includes the calling role (e.g. `pi-hole`) and then this role.
```
- name: Setup pi-hole docker containers
  hosts: all
  gather_facts: yes
  become: yes

  roles:  # Among other, automatically run tasks/main and handlers/main of these roles.
    - pi-hole
    - docker-common
```

### Setup calling role
The calling role (e.g. `pi-hole`) needs a `tasks/main.yaml` file that defines the necessary variables.
```
- set_fact:
    service_name: "{{ role_name }}"
    service_files_path_prefix: "{{ role_path }}"
```

### Run the target playbook
Run the playbook (in the target role), e.g.
```
play -i inventory.yaml -l <group> roles/cloudflare-dns-updater/setup.yaml
```

### Get fancy with Traefik
Add the role `traefik` to check if your main `docker-compose.yaml` file defines a network `traefik` and start the traefik service 
if neccesary.
```
- name: Setup pi-hole docker containers
  hosts: all
  gather_facts: yes
  become: yes

  roles:  # Among other, automatically run tasks/main and handlers/main of these roles.
    - pi-hole
    - docker-common
    - check-traefik
```