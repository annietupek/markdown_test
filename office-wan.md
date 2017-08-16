# Office WAN

Table of Contents

+ [Overview](#overview)
+ [Scenarios](#scenarios)
+ [Usage](#usage)
+ [Prerequisites](#prerequisites)
	+ [Running the playbooks](#running-the-playbooks)
	+ [Bootstrap OPNsense](#bootstrap-opnsense)
		+ [Outcome](#outcome-bootstrap)
	+ [Provision OPNsense](#provision-opnsense)
		+ [Outcome](#outcome-provision)

# Overview

[OPNsense](https://opnsense.org/) is our firewall and routing platform.

This ansible project automates the provisioning and configuration of OPNsense on
	the PolySync network.

# Scenarios

* [Bootstrap OPNSense](#bootstrap-opnsense)
* [Provision OPNSense](#provision-opnsense)

# Usage
Assumes the polysync-ansible repository is in the user's home directory.
If it is elsewhere, change directory as appropriate.

## Prerequisites

You must have Ansible installed to run these playbooks.
See the top-level [README](../README.md) for instructions on installing Ansible.

You must have admin access to the network.

## Running the playbooks

* ## Bootstrap OPNsense

	This playbook bootstraps OPNSense on the main router by installing the Ansible
		sudoer template and the OPNSense config file.

		$ cd ~/polysync-ansible/office-wan
		$ ansible-playbook -i inventory/hosts bootstrap.yml

	### <a name="outcome-bootstrap"></a>Outcome

	* After this playbook is run, the OPNsense firewall should be bootstrapped on the
		PolySync network.

* ## Provision OPNsense

	This playbook provisions OPNSense on the Jack and Janet machines by installing
		the Ansible sudoer template and the OPNSense config file.

		$ cd ~/polysync-ansible/office-wan
		$ ansible-playbook -i inventory/hosts provision.yml

	### <a name="outcome-provision"></a>Outcome

	* After this playbook is run, the OPNsense firewall should be configured on the
		PolySync network.
