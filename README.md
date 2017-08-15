# Employee Laptop Provisioning

Table of Contents

* [Overview](#Overview)
* [Scenarios](#Scenarios)
* [Usage](#Usage)
* [Prerequisites](Prerequisites)
	* [Running the playbooks](##Running-the-playbooks)
	* [bootstrap.yml](#bootstrap.yml)
		* [Outcome](#Outcome_bootstrap)
	* [Common to all playbooks](#Common-to-all-provisioning-playbooks)
		* [Outcome](#Outcome_common)
	* [provision_core.yml](#provision_core.yml)
		* [Outcome](#Outcome_core)
	* [provision_enablement.yml](#provision_enablement.yml)
		* [Outcome](#Outcome_enablement)
	* [provision_helios.yml](#provision_helios.yml)
		* [Outcome](#Outcome_helios)
	* [provision_oscc.yml](#provision_oscc.yml)
		* [Outcome](#Outcome_oscc)
	* [Additional Information](#Additional-Information)

# Overview

This ansible project automates the provisioning of employee laptops so that all
	teams are developing software within a consistent environment.

Each team within PolySync has dedicated playbooks for provisioning the specific
	tools they need.

After these playbooks are run, the employee laptop will be correctly provisioned
	with their needed dependencies and security settings.

# Scenarios

This project has both common and team-specific playbooks.  Everyone should run
	the bootstrap playbook.  You should also run the playbook for your respective
	team.

* [bootstrap](#bootstrap.yml)
* [Core](#provision_core.yml)
* [Enablement](#provision_enablement.yml)
* [Helios](#provision_helios.yml)
* [OSCC](#provision_oscc.yml)

# Usage
Assumes the polysync-ansible repository is in the user's home directory.
If it is elsewhere, change directory as appropriate.

## Prerequisites

You must have Ansible installed to run these playbooks.
See the top-level [README](../README.md) for instructions on installing Ansible.
You must also have the sudo privileges for the machine you are running the playbooks
	on.

## Running the playbooks

* ## bootstrap.yml

	This playbook is common to all teams.  It should be run before the team-specific
		playbook.  The `-K` flag will force Ansible to prompt for **your** password
		so it can escalate its permissions while executing the playbook.

		$ cd ~/polysync-ansible/employee-laptop
		$ ansible-playbook bootstrap.yml -K

	### <a name="Outcome_bootstrap">Outcome</a>

	* Creates an `ansible` user for later automation and configures the admins
		(Engineering Enablement Team) who can connect to the machine so future
		maintenance work can be completed as swiftly as possible.
	* Yubikey rules are setup on the machine so that signed commits can be made and
		the machine will lock on Yubikey removal.

* ## Common to all provisioning playbooks

	* All team-specific playbooks call the workstation role's install and laptop
		tasks and the builder role's install task.

	### <a name="Outcome_common"></a>Outcome

	* The workstation and builder install tasks install tools and dependencies
		common to all teams:
		* Virtualbox
		* Slack
		* Zoom
		* apt
		* Git, Git LFS
		* pip, python
		* Ansible
	* The workstation laptop task installs additional packages for laptop-specific
		things like improved power management.

* ## provision_core.yml

	This playbook installs the Core Team dependencies.  The `-K` flag will force
		Ansible to prompt for **your** password so it can escalate its permissions
		while executing the playbook.

		$ ansible-playbook provision_core.yml -K

	### <a name="Outcome_core"></a>Outcome

	Core Team dependencies will be installed on the employee laptop.
	These include:

	* Build dependencies
	* Cross compilation toolchains
	* Test dependencies
	* pip
	* Conan
	* Rust
	* ROS dependencies

* ## provision_enablement.yml

	This playbook installs the Engineering Enablement Team dependencies. The `-K`
		flag will force Ansible to prompt for **your** password so it can escalate
		its permissions while executing the playbook.

		$ ansible-playbook provision_enablement.yml -K

	### <a name="Outcome_enablement"></a>Outcome

	Engineering Enablement Team dependencies will be installed on the employee laptop.
	These include:

	* Erlang
	* Elixir
	* Packer

* ## provision_helios.yml

	This playbook installs the Helios Team dependencies.  The `-K` flag will force
		Ansible to prompt for **your** password so it can escalate its permissions
		while executing the playbook.

		$ ansible-playbook provision_helios.yml -K


	### <a name="Outcome_helios"></a>Outcome

	Helios Team dependencies will be installed on the employee laptop.
	These include:

	* Rust

* ## provision_oscc.yml

	This playbook installs the OSCC Team dependencies.  The `-K` flag will force
		Ansible to prompt for **your** password so it can escalate its permissions
		while executing the playbook.

		$ ansible-playbook provision_oscc.yml -K

	### <a name="Outcome_oscc"></a>Outcome

	OSCC Team dependencies will be installed on the employee laptop.
	These include:

	* Build dependencies
	* Test dependencies
	* Rust

## Additional Steps
If the employee already has their Yubikey and gpg set up, additional configuration
	commands will need to be run.

These commands already exist in gpg role tasks, however they have not yet been
	integrated into a specific playbook for reprovisioing an employee laptop.

From the terminal, you'll need to run the following commands:
```
git config --global user.name "<Your First and Last Name>"
git config --global user.email "<Your Email Address>"
git config --global commit.gpgSign true
git config --global gpg.program gpg2
git config --global push.gpgSign if-asked
git config --global user.signingKey <Your Public Key ID>

gpg2 --list-keys
gpg2 --keyserver pgp.mit.edu --recv-keys <Your Public Key ID>
gpg2 --expert --edit-key <Your Public Key ID> trust
gpg2 --card-status
```