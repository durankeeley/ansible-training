To set up the Ansible inventory file and playbook to use a secrets file, you will need to store the secrets (such as the username and password for the Cisco switches) in a separate file and reference them in the inventory file and playbook using the `ansible-vault` command.

First, create a secrets file with the secrets that you want to use. For example, you can create a secrets file called `secrets.yml` with the following content:

```
ansible_user: admin
ansible_password: secret
```

Next, create an encrypted version of the secrets file using the `ansible-vault` command. For example:

```
ansible-vault encrypt secrets.yml
```

This will prompt you for a password to use for encrypting the secrets file. Enter a strong password and press Enter. The secrets file will be encrypted and saved as `secrets.yml.gpg`.

Next, update the inventory file to reference the encrypted secrets file. For example:

```
[cisco_switches]
switch1 ansible_host=192.168.1.1 ansible_vault_password_file=secrets.yml.gpg
switch2 ansible_host=192.168.1.2 ansible_vault_password_file=secrets.yml.gpg
switch3 ansible_host=192.168.1.3 ansible_vault_password_file=secrets.yml.gpg
```

The `ansible_vault_password_file` variable specifies the path to the encrypted secrets file, and tells Ansible to use the password stored in the file to decrypt the secrets when running the playbook.

Finally, update the playbook to reference the secrets in the inventory file. For example:

```
- hosts: cisco_switches
  tasks:
  - name: Configure VLAN 10
    cisco.ios.ios_config:
      lines:
        - vlan 10
        - name VLAN10
  - name: Configure VLAN 20
    cisco.ios.ios_config:
      lines:
        - vlan 20
        - name VLAN20
  - name: Save configuration
    cisco.ios.ios_config:
      save_when: modified
```

To run the playbook, you will need to provide the password for the encrypted secrets file using the `--ask-vault-pass` option:

```
ansible-playbook -i inventory.ini --ask-vault-pass playbook.yml
```

Ansible will prompt you for the password, and then use it to decrypt the secrets file and run the playbook using the username and password specified in the secrets file.
