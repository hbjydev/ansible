# Ansible

Ansible playbooks, roles, modules, etc. are saved in this repository.

Each playbook should include comments or a name descriptor that explains its
purpose or usage. If unavailable, README-... files can be used instead, to give
more detail than comments would.

## Management Node Structure

On your Ansible node, the structure should look like this:

```
.
├── ansible.cfg
├── collections
├── files -> playbooks/files
├── handlers -> playbooks/handlers
├── inventories
│   ├── production
│   |   ├── group_vars
│   |   ├── host_vars
│   |   hosts
│   ├── staging
│   ├── devellopment
├── pkistore
├── playbooks
│   ├── files
│   ├── handlers
│   ├── tasks
│   ├── templates
│   ├── vars
├── roles/local
│   └── <role-name>
│   └── requirements.yml
├── tasks -> playbooks/tasks
├── templates -> playbooks/templates
└── vars -> playbooks/vars
```

## Structure

What each folder in this repository represents:

```
files       -> As the name implies, this folder will store static (untemplated)
               files that are installed on the target operating system. This
               folder should be structured to represent the filesystem.
group_vars  -> Group variables go here if not defined in the inventory. It
               is generally preferred over inventory vars.
host_vars   -> Host-specific variables go here if not defined in the inventory.
               It is generally preferred over inventory vars.
inventories -> All static inventories go here.
roles       -> Custom roles can go here
tasks       -> Common tasks can go here
templates   -> Template files go here, in the same structure as files use.
vars        -> Global variables called from within a playbook can go in here.
```

## Playbook Design

```yml
---
- name: 'Descriptive name for the playbook'
  hosts: '{{ host }}'

  handlers:
    - include: handlers/main.yml

  pre_tasks:
    - name: Check if ansible cannot be run here
      stat:
        path: /etc/no-ansible
      register: no_ansible

    - name: Verify if we can run ansible
      assert:
        that:
          - "not no_ansible.stat.exists"
        success_msg: "We are able to run on this node"
        fail_msg: "/etc/no-ansible exists - skipping run on this node"

  # Import roles/tasks here

  post_tasks:
    - name: Touching run file that ansible has ran here
      file:
        path: /var/log/ansible.run
        state: touch
        mode: '0644'
        owner: root
        group: root
```

