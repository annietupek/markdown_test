# Office Builders

Table of Contents

+ [Overview](#overview)
+ [Scenarios](#scenarios)
+ [Usage](#usage)
+ [Prerequisites](prerequisites)
	+ [Running the playbooks](#running-the-playbooks)
	+ [Provisioning build machines](#provisioning-build-machines)
		+ [Outcome](#outcome-build-machines)
	+ [Resetting the Conan Cache](#resetting-the-conan-cache)
		+ [Outcome](#outcome-reset-conan)

# Overview

This ansible project automates the provisioning and management of our build and
	test machines.

It sets up the Jenkins build system and includes a playbook for resetting the
	Conan cache which can be run as needed.

# Scenarios

* [Provision build machines](#provisioning-build-machines)
* [Reset Conan Cache](#resetting-the-conan-cache)

# Usage
The examples assume the polysync-ansible repository is in the user's home directory.
If it is elsewhere, change directory as appropriate.

## Prerequisites

You must have Ansible installed to run these playbooks.
See the top-level [README](../README.md) for instructions on installing Ansible.

## Running the playbooks

* ## Provisioning build machines

	This playbook sets up the build machines with Jenkins and all necessary
		dependencies for building PolySync projects.

		$ cd ~/polysync-ansible/office-builders
		$ ansible-playbook -i inventory/hosts bootstrap.yml
		$ ansible-playbook -i inventory/hosts provision.yml

	### <a name="outcome-build-machines"></a>Outcome:

	* After these playbooks are run, the build machines will be provisioned with the
		tools necessary to build Jenkins jobs.

* ## Resetting the conan cache

	PolySync Core uses Conan for managing package dependencies and maintains a package
		cache.  Because we build for seven architectures on four different operating
		systems, we have 28 different Conan caches that need to be managed.

	The Conan cache can be corrupted for a couple of reasons:

	* When Conan is updated
	* When a Jenkins job gets cancelled in the middle of pulling down the Conan cache
	* When changes are made to part of the Conan process.

	The corrupted Conan cache will cause problems with the build process and will
		need to be reset.

	To reset the conan cache:


		$ cd ~/polysync-ansible/office-builders
		$ ansible-playbook -i inventory/hosts cache-reset.yml


	### <a name="outcome-reset-conan"></a>Outcome:

	* After this playbook is run, the Conan cache will be deleted and re-initialized
		for the next time a build is run.
