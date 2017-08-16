# PolySync Key Ceremony

Table of Contents

+ [Overview](#overview)
    + [Terminology](#terminology)
+ [Scenarios](#scenarios)
+ [Usage](#usage)
+ [Prerequisites](#prerequisites)
    + [Running the playbooks](#running-the-playbooks)
    + [Root Key Generation](#root-key-generation)
        + [Outcome](#outcome_root_key_gen)
    + [Yubikey Provisioning](#yubikey-provisioning)
        + [Outcome](#outcome_yubikey_provisioning)
    + [Employee Key Generation and Security Device Provisioning](#employee-key-generation-and-security-device-provisioning)
        + [Outcome](#outcome_employee_key_gen)
    + [Change Root Key Passphrase](#change-root-key-passphrase)
        + [Outcome](#outcome_change_root_key_passphrase)
    + [Change Executive Key Passphrase](#change-executive-key-passphrase)
        + [Outcome](#outcome_change_exec_key_passphrase)
    + [Change Employee Key Passphrase](#change-employee-key-passphrase)
        + [Outcome](#outcome_change_employee_key_passphrase)
    + [Root Certifies Executive Key](#root-certifies-executive-key)
        + [Outcome](#outcome_root_certifies_exec)
    + [Executive Certifies Employee Key](#executive-certifies-employee-key)
        + [Outcome](#outcome_exec_certifies_employee)
    + [Synchronize HSMs](#synchronize-hsms)
        + [Outcome](#outcome_sync_hsms)
    + [Additional Information](#additional-information)

# Overview

This ansible project automates the security processes creating, extending, and
maintaining security keys.

## Terminology

NOTE: Most cryptographic texts overload the term *signing* to refer to both
payload signing and key signing. These actions use different portions of a
keyset. Payload signing is done with the *sign* authorization, which may be
applied both to a master key or a subkey. Key signing is done with the
*certify* authorization, which is only available to the master key. Because
one may be done with a subkey and the other may not, this document will
use different terms, *signing* and *certifying* respectively, to distinguish
between these circumstances since signing is a common action that may be done
with an security device while certifying is an uncommon action that will require
access to the master private key.

 - **Root Key** : The keyset representing the company. Only used for certifying
   other keys.
 - **Keyset** : The collection of public and private keys associated with an
   identity.
 - **Master Key** : The main key pair associated with an identity. Easy to
   revoke, but a hassle to transition to new master key.
 - **Subkey** : A key pair derived from a master key. Usually authorized for
   only a specific action, such as encryption, signing, or authentication. Easy
   to revoke and reissue.
 - **Sign** : Create an artifact from the data payload and your keyset that
   others can verify to assert the data payload has not been modified. Does not
   require concealment of data payload. Anyone can read and verify the
   signature.
 - **Encrypt** : Create a concealed artifact from the data payload that can only
   be read by the intended recipient.


## Scenarios

This project has several playbooks depending on the situation.

* [Root Key Generation](#root-key-generation)
* [Yubikey Provisioning](#yubikey-provisioning)
* [Employee Key Generation amd Security Device Provisioning](#employee-key-generation-and-security-device-provisioning)
* [Change Root Key Passphrase](#change-root-key-passphrase)
* [Change Executive Key Passphrase](#change-executive-key-passphrase)
* [Change Employee Key Passphrase](#change-employee-key-passphrase)
* [Root Certifies Executive Key](#root-certifies-executive-key)
* [Executive Certifies Employee Key](#executive-certifies-employee-key)
* [Synchronize HSMs](#synchronize-hsms)
* [Lost Security Device](#additional-information)
* [Forgotten Master Passphrase](#additional-information)
* [Renew Employee Key](#additional-information)


## Usage
Assumes the polysync-ansible repository is in the user's home directory.
If it is elsewhere, change directory as appropriate.

For all playbooks, there is a two minute timeout for typing the passphrase.

## Prerequisites

You must have Ansible installed to run these playbooks.
See the top-level [README](../README.md) for instructions on installing Ansible.

## Running the playbooks

* ## Root Key Generation

    This playbook generates the PolySync Root Key, installs it on the HSM, and
      publishes the Public key to the keyserver.

    It requires the following preconditions to be met:

    * HSM unlocked
    * Initialized Git repo in <Root key directory> on HSM

        $ cd ~/polysync-ansible/key-ceremony
        $ ansible-playbook -i inventory/hosts gpg_gen_root_key.yml

    ### <a name="outcome_root_key_gen"></a>Outcome

    * New PolySync Root Key
    * New commit in the <Root Key directory> Git repo
    * Public key publlished to pgp.mit.edu
    * HSM unlocked

* ## Yubikey Provisioning

    This playbook pre-provisions a Yubikey security device

    It requires the following preconditions to be met:

    * Asset Issue Created in JIRA:

        * Select the Asset Management project
        * Create issue
        * Summary: Yubikey 4
        * Set Date of Purchase
        * Set Manufacturer: Yubico
        * Set Purchase Price
        * Set Serial Number
        * Assign to Yubikey recipient

        $ cd ~/polysync-ansible/key-ceremony
        $ ansible-playbook -i inventory/hosts provision_yubikey.yml

    ### <a name="outcome_yubikey-provisioning"></a>Outcome

    * Yubikey pre-provisioned for employee

* ## Employee Key Generation and Security Device Provisioning

    This playbook generates the employee gpg key and exports it to the employee's Yubikey.

    It also configures the employee's git to use signed commits and sets up their local gpg.

    It requires the following preconditions to be met:

    * Pre-provisioned security device
    * Employee photo (roughly 200x200 pixels and ~6KiB)
    * Public key ID for encrypted bundle recipient (Executive?)
    * Public key for encrypted bundle recipient published to pgp.mit.edu

        $ cd ~/polysync-ansible/key-ceremony
        $ ansible-playbook -i inventory/hosts gpg_gen_key.yml

    ### <a name="outcome_employee_key_gen"></a>Outcome

    * New keys for employee
    * Git pre-configured for employee
    * GPG pre-configured for employee
    * Employee public key published to pgp.mit.edu
    * Encrypted bundle exists and can be commited to repo

* ## Change Root Key Passphrase

    **Right now we just have a task change_passphrase.yml in the gpg role**

    It requires the following preconditions to be met:

    * HSM unlocked
    * Existence of Root Key

        $ cd ~/polysync-ansible/key-ceremony
        $ ansible-playbook -i inventory/hosts <name_of_playbook>.yml

    ### <a name="outcome_change_root_key_passphrase"></a>Outcome

    * Changed Root Key passphrase
    * New commit in <Root Key directory> Git repo
    * HSM unlocked

* ## Change Executive Key Passphrase

    This playbook changes an executive key's passphrase.

    It requires the following preconditions to be met:

    * HSM unlocked
    * Encrypted Executive bundle in <Executive Key directory>
    * Root Key passphrase available

        $ cd ~/polysync-ansible/key-ceremony
        $ ansible-playbook -i inventory/hosts change_executive_passphrase.yml

    ### <a name="outcome_change_exec_key_passphrase"></a>Outcome

    * Changed Executive Key passphrase
    * Updated Encrypted Executive bundle in <Executive Key directory>
    * New commit in <Executive Key directory> Git repo
    * HSM unlocked

* ## Change Employee Key Passphrase

    **Right now we just have a task change_passphrase.yml in the gpg role**

    It requires the following preconditions to be met:

    * Executive-encrypted bundle of exported Employee keys
    * Executive with security device containing encryption keys

        $ cd ~/polysync-ansible/key-ceremony
        $ ansible-playbook -i inventory/hosts <name_of_playbook>.yml

    ### <a name="outcome_change_employee_key_passphrase"></a>Outcome

    * Changed Employee Key passphrase

* ## Root Certifies Executive Key

    This playbook certifies an executive's key with the PolySync Root Key

    It requires the following preconditions to be met:

    * HSM unlocked
    * Public Key ID of the executive to be certified

        $ cd ~/polysync-ansible/key-ceremony
        $ ansible-playbook -i inventory/hosts root_certifies_executive_key.yml

    ### <a name="outcome_root_certifies_exec"></a>Outcome

    * Root-certified executive public keys published to pgp.mit.edu
    * HSM unlocked

* ## Executive Certifies Employee Key

    This playbook certifies an employee's key with an executive's key

    It requires the following preconditions to be met:

    * HSM unlocked
    * Root Key passphrase available
    * Employee's public key ID

        $ cd ~/polysync-ansible/key-ceremony
        $ ansible-playbook -i inventory/hosts executive_certifies_employee_key.yml

    ### <a name="outcome_exec_certifies_employee"></a>Outcome

    * Executive-certified employee public key published on pgp.mit.edu
    * HSM unlocked

* ## Synchronize HSMs

    This playbook synchonizes the HSMs.

    It requires the following preconditions to be met:

    * Unlocked HSMs
    * Executive (Root?) passphrase

        $ cd ~/polysync-ansible/key-ceremony
        $ ansible-playbook -i inventory/hosts executive_synchonize_hsm.yml

    ### <a name="outcome_sync_hsms"></a>Outcome

    * HSMs synchonized

## Additional Information:
**TODO:**

We are missing playbooks for:

* Change root key passphrase
* Change employee key passphrase
* Lost security device
* Forgotten master passphrase
* Renew employee key