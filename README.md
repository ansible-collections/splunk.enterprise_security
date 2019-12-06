# Splunk Enterprise Security Ansible Collection

## Tech Preview

Using splunk modules are meant to be used with the [`httpapi` connection
plugin](https://docs.ansible.com/ansible/latest/plugins/connection/httpapi.html)
and as such we will set certain attributes in the inventory

Example `inventory.ini`:

NOTE: The passwords should be stored in a secure location or an [Ansible
Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html)

NOTE: the default port for Splunk's REST API is 8089


    [splunk]
    splunk.example.com

    [splunk:vars]
    ansible_network_os=splunk
    ansible_user=admin
    ansible_httpapi_pass=my_super_secret_admin_password
    ansible_httpapi_port=8089
    ansible_httpapi_use_ssl=yes
    ansible_httpapi_validate_certs=True
    ansible_connection=httpapi


Example playbook:

License
-------

GPLv3

Author Information
------------------

[Ansible Security Automation Team](https://github.com/ansible-security)
This is the [Ansible
Collection](https://docs.ansible.com/ansible/devel/collections_tech_preview.html)
provided by the [Ansible Security Automation
Team](https://github.com/ansible-security) for automating actions in [Splunk
Enterprise Security
SIEM](https://www.splunk.com/en_us/software/enterprise-security.html)

This Collection is meant for distribution via
[Ansible Galaxy](https://galaxy.ansible.com/) as is available for all
[Ansible](https://github.com/ansible/ansible) users to utilize, contribute to,
and provide feedback about.

### Using Splunk Enterprise Security Ansible Collection

An example for using this collection to manage a log source with [Splunk Enterprise Security SIEM](https://www.splunk.com/en_us/software/enterprise-security.html) is as follows.

`inventory.ini` (Note the password should be managed by a [Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) for a production environment.
```
[splunk]
splunk.example.com

[splunk:vars]
ansible_network_os=splunk.enterprise_security.splunk
ansible_user=admin
ansible_httpapi_pass=my_super_secret_admin_password
ansible_httpapi_port=8089
ansible_httpapi_use_ssl=yes
ansible_httpapi_validate_certs=True
ansible_connection=httpapi
```

#### Define your collection search path at the Play level

Below we specify our collection at the Play level which allows us to use the
splunk modules without specifying the need for the
Ansible Collection Namespace.

`splunk_with_collections_example.yml`
```
---
- name: demo splunk
  hosts: splunk
  gather_facts: False
  collections:
    - splunk.enterprise_security
  tasks:
    - name: test splunk_data_input_monitor
      ansible_security.collection.splunk_data_input_monitor:
        name: "/var/log/demo.log"
        state: "present"
        recursive: True
    - name: test splunk_data_input_network
      ansible_security.collection.splunk_data_input_network:
        name: "9001"
        protocol: "tcp"
        state: "absent"
    - name: test splunk_coorelation_search
      ansible_security.collection.splunk_correlation_search:
        name: "Test Demo Coorelation Search From Playbook"
        description: "Test Demo Coorelation Search From Playbook, description."
        search: 'source="/var/log/snort.log"'
        state: "present"
    - name: test splunk_adaptive_response_notable_event
      ansible_security.collection.splunk_adaptive_response_notable_event:
        name: "Demo notable event from playbook"
        correlation_search_name: "Test Demo Coorelation Search From Playbook"
        description: "Test Demo notable event from playbook, description."
        state: "present"
        next_steps:
          - ping
          - nslookup
        recommended_actions:
          - script
```

#### Define your collection search path at the Block level

Below we use the [`block`](https://docs.ansible.com/ansible/latest/user_guide/playbooks_blocks.html)
level keyword, we are able to use the splunk modules without the need for the
Ansible Collection Namespace.

`splunk_with_collections_block_example.yml`
```
---
- name: demo splunk
  hosts: splunk
  gather_facts: False
  tasks:
    - name: collection namespace block
      - name: test splunk_data_input_monitor
        ansible_security.collection.splunk_data_input_monitor:
          name: "/var/log/demo.log"
          state: "present"
          recursive: True
      - name: test splunk_data_input_network
        ansible_security.collection.splunk_data_input_network:
          name: "9001"
          protocol: "tcp"
          state: "absent"
      - name: test splunk_coorelation_search
        ansible_security.collection.splunk_correlation_search:
          name: "Test Demo Coorelation Search From Playbook"
          description: "Test Demo Coorelation Search From Playbook, description."
          search: 'source="/var/log/snort.log"'
          state: "present"
      - name: test splunk_adaptive_response_notable_event
        ansible_security.collection.splunk_adaptive_response_notable_event:
          name: "Demo notable event from playbook"
          correlation_search_name: "Test Demo Coorelation Search From Playbook"
          description: "Test Demo notable event from playbook, description."
          state: "present"
          next_steps:
            - ping
            - nslookup
          recommended_actions:
            - script
      collections:
        - splunk.enterprise_security
```

### Directory Structure

* `docs/`: local documentation for the collection
* `license.txt`: optional copy of license(s) for this collection
* `galaxy.yml`: source data for the MANIFEST.json that will be part of the collection package
* `playbooks/`: playbooks reside here
  * `tasks/`: this holds 'task list files' for `include_tasks`/`import_tasks` usage
* `plugins/`: all ansible plugins and modules go here, each in its own subdir
  * `modules/`: ansible modules
  * `lookups/`: lookup plugins
  * `filters/`: Jinja2 filter plugins
  * ... rest of plugins
* `README.md`: information file (this file)
* `roles/`: directory for ansible roles
* `tests/`: tests for the collection's content
