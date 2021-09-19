# Samba

## Requirements

- Ansible 2.9 and the following collections:
  - ansible.posix (seboolean, firewalld)
  - community.general (semanage, fcontext)

## Configuration

### Variables

Configure variables according to your needs in [vars/config.yml](vars/config.yml)

`samba_usergroup`

- Users in this group will be granted access to the file server.

`samba_public_dir`

`samba_private_dir`

- Paths to public and private samba folders.

`samba_configuration_dir`

`samba_configuration`

- Paths to the configuration folder and the configuration file: `smb.conf`.

`samba_users`

```yaml
  - name:
    password:
    samba_password:
```

- Users will be created and assigned a password to access the file server.

### Generate encrypted password

To easily generate an encrypted password, use the crypt python library:

`python -c 'import crypt; print crypt.crypt("password", "$1$salt$")'`

## How-to

1. Install ansible. See: [https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html](Installing Ansible ─ Ansible Documentation)
2. Install required collections: `ansible-galaxy collection install ansible.posix community.general`
3. Clone this repository: `git clone https://github.com/miguelguzmanr/ansible-samba`
4. Run your playbook: `cd ansible-samba && ansible-playbook ./samba.yml`

## Example Output

```
PLAY [Deploy and configure a Samba server] *********************************************************

TASK [include_vars] ********************************************************************************
ok: [192.168.1.199]

TASK [Install the latest version of Samba] *********************************************************
ok: [192.168.1.199]

TASK [Create the user group if it does not exist] **************************************************
ok: [192.168.1.199]

TASK [Create the public directory if it does not exist] ********************************************
ok: [192.168.1.199]

TASK [Create the private directory if it does not exist] *******************************************
ok: [192.168.1.199]

TASK [Create a new Samba configuration file] *******************************************************
ok: [192.168.1.199]

TASK [Start and enable the Samba service] **********************************************************
ok: [192.168.1.199] => (item=smb)
ok: [192.168.1.199] => (item=nmb)

TASK [Set Samba related flags on SELinux and keep it persistent across reboots] ********************
ok: [192.168.1.199] => (item=samba_export_all_ro)
ok: [192.168.1.199] => (item=samba_export_all_rw)

TASK [Allow Samba to modify files in the configured directories] ***********************************
ok: [192.168.1.199] => (item=/samba/public)
ok: [192.168.1.199] => (item=/samba/private)

TASK [Apply new SELinux file context to filesystem] ************************************************
changed: [192.168.1.199] => (item=/samba/public)
changed: [192.168.1.199] => (item=/samba/private)

TASK [Allow Samba traffic in the public zone] ******************************************************
ok: [192.168.1.199]

TASK [Reload the Firewall service] *****************************************************************
changed: [192.168.1.199]

TASK [Create Samba users if they don't exist] ******************************************************
ok: [192.168.1.199] => (item={'name': 'miguel', 'password': '(REDACTED)', 'samba_password': '(REDACTED)'})

TASK [Grant users access to Samba] ******************************************************************
changed: [192.168.1.199] => (item={'name': 'miguel', 'password': '(REDACTED)', 'samba_password': '(REDACTED)'})

PLAY RECAP *****************************************************************************************
192.168.1.199              : ok=14   changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```
