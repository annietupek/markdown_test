**Roles**

Table of Contents

+ [Overview](#overview)
+ [Roles](#roles)
+ [Usage](#usage)
	+ [main.yml Task](#mainyml-task)

# Overview

This directory contains the different roles that ansible playbooks call.

See the README in each directory for more information about the tasks contained
	within each role.

# Roles

* ansiblee
* builder
* gpg2
* opnsense
* workstation

# Usage

Ansible roles are a way to organize tasks, variable files, and configuration files
	for ansible playbooks.  A role is run in an ansible playbook by passing a list
	of tasks as the value of a role variable called run.

Example:

```yaml
- hosts: localhost
  roles:
    - { role: gpg2, run: ['install', 'setup_udev'] }
```
This example playbook calls the gpg2 role and has it execute two tasks: install
	and setup_udev.  Tasks are run in the order in which they are listed.

## <a name="mainyml-task"></a>main.yml Task

Each role should have a main.yml task.  This is the role task runner.
Its purpose is to allow calling into the role vial the `roles:` shorthand
from the playbook level (see the above example).

The main.yml file should only contain:
```YAML
- name: role task runner
  include: "{{ subtask_file }}.yml"
  with_items: "{{ run|default([]) }}"
  when: "run != []"
  loop_control:
    loop_var: subtask_file
```
