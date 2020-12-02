# f5

[![CI](https://github.com/VeselaHouba/f5-ansible/workflows/CI/badge.svg)](https://github.com/VeselaHouba/f5-ansible/actions?query=workflow%3ACI)

Ansible role, which makes managing F5 boxes (physical/virtual) little bit less painful, by adding some logic to existing ansible [F5 modules](https://docs.ansible.com/ansible/latest/modules/list_of_network_modules.html#f5)

## How to make it work

### Docker version (recommended)
Extra GitHub repository `VeselaHouba/f5-docker` with docker wrapper has been created to ease your pain. Check [this URL](https://github.com/VeselaHouba/f5-docker) and then come back for detailed explanation of how to define your variables.


### Virtualenv version (deprecated)
If you don't want to mess with your default env, install everything in virtualenv. It might be good idea to actually wrap everything in docker.

```bash
pip install vitualenv
virtualenv ansible-dev
source ansible-dev/bin/activate
pip install --user f5-sdk
pip install git+https://github.com/ansible/ansible.git@devel
```

Next time you just need to activate it again

```bash
source ansible-dev/bin/activate
```

## Variables and files explained

### Role Variables
Check default values in `defaults/main.yml`. This project is currently under heavy development, so documentation is not maintained. It will be filled once the project changes settle down.

#### iRules
You can define path for iRule relative to your playbook

```yaml
f5_iRules_list:
  - { name: irule_ib-block_ip, file: ../files/iRules/irule_ib-block_ip.tcl}
```

Or use one of pre-defined iRules inside this role `files/iRules/` and reference only by it's name

```yaml
f5_iRules_list:
  - { name: irule_example }
```

#### iApps

Upload templates and create instances (services) of iApps. You have to check insides of the iApp template to check variables names. (You're basically bypassing the visual part of iApp)

Following is example of iApp to periodically update CRL from public site.

```yaml
f5_iApps_templates:
  - name: automated_crl_update
    path: ../files/iApps/automated_crl_update.tmpl
    state: present

f5_iApps_services:
  - name: update_crl_from_ICA
    template: automated_crl_update
    state: present
    parameters:
      variables:
        - name: "crl_configuration__name"
          value: "ICA_crl"
        - name: crl_configuration__interval
          value: 3600
      tables:
        - name: "crl_configuration__url_list"
          columnNames:
            - url
          rows:
            - row:
                - http://qcrldp1.ica.cz/rca15_rsa.crl

```


### Partitions
If you want to use specific partitions, then you have to define absolute paths to cross-partition resources. Only resources in the same partition are searched without prefix.

```YAML
f5_partitions:
  - name: test_part
    description: testing partition

f5_virtual_servers:
  - name: part_test
    destination: 10.0.0.1
    port: 80
    pool: pool_part_test
    profiles:
      - /Common/some_profile
      - name: /Common/other_profile
        context: client-side
      - name: /Common/tcp-wan-optimized
        context: client-side
      - name: /Common/tcp-lan-optimized
        context: server-side
    enabled_vlans:
      - /Common/some_vlan
    irules:
      - /Common/some_irule_in_common
      - some_irule_in_test_part
    partition: test_part
```

### Partial Deploy
This feature was added to support ability to deploy only some parts of infrastructure. For example if you want to deploy only single virtual host, put it's name to `f5_partial_deploy` variable. Module will then skip all tasks except virtual host deploy. Any supported name can be used: iRule, username, pool name, profile, ...

Examples:
```bash
# single virtual server
ansible-playbook playbooks/deploy.yml -e "f5_partial_deploy=my_virtual_server"
# multiple items
ansible-playbook playbooks/deploy.yml -e "f5_partial_deploy=my_virtual_server,my_pool"
```

You can also add your variables to file and load it from command line
```bash
$ cat f5_partial_deploy.yml

---
f5_partial_deploy:
  - my_pool
  - my_virtual_server
  - my_http_profile
  - my_asm_profile

ansible-playbook playbooks/deploy.yml -e @f5_partial_deploy.yml
```

### Profiles

#### Rewrite profile
F5 official modules don't provide rewrite profile deploy function (yet), so implementation in python is in library. Credits @erjac77
```YAML
- name: profile_rewrite_01
  type: rewrite
  rewrite_mode: uri-translation
  uri_rules:
    - name: test
      type: both
      client:
        path: /old_path/
      server:
        path: /old_path/
```


## License

BSD

## Author Information
Jan Michalek (michalek_at_m-cloud.cz)
