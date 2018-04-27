DCB role
========

This role facilitates the configuration of data center bridging (DCB). It supports the configuration of the DCB map and the DCB buffer, and assigns them to interfaces. This role is abstracted for dellos9.

The DCB role requires an SSH connection for connectivity to a Dell EMC Networking device. You can use any of the built-in OS connection variables .

Installation
------------

    ansible-galaxy install Dell-Networking.dellos-dcb

Role variables
--------------

- Role is abstracted using the *ansible_network_os*/ *ansible_net_os_name* variable and can take the dellos9 value
- If *dellos_cfg_generate* is set to true, generates the role configuration commands in a file
- Any role variable with a corresponding state variable set to absent negates the configuration of that variable
- Setting an empty value for any variable negates the corresponding configuration
- Variables and values are case-sensitive

**dellos_dcb keys**

| Key        | Type                      | Description                                             | Support               |
|------------|---------------------------|---------------------------------------------------------|-----------------------|
| ``dcb_enable`` | boolean: true,false | Enables/disables DCB | dellos9 |
| ``dcb_map`` | list | Configures the DCB map (see ``dcb_map.*``) | dellos9 |
| ``dcb_map.name`` | string (required)         | Configures the DCB map name  | dellos9 |
| ``dcb_map.priority_group`` | list           | Configures the priority-group for the DCB map (see ``priority_group.*``) | dellos9 |
| ``priority_group.pgid`` | integer (required): 0-7        | Configures the priority-group ID                     | dellos9 |
| ``priority_group.bandwidth`` | integer (required)         | Configures the bandwidth percentage for the priority-group         | dellos9 |
| ``priority_group.pfc`` | boolean: true,false (required)         | Configures PFC on/off for the priorities in the priority-group | dellos9 |
| ``priority_group.state`` | string: absent,present\*   | Deletes the priority-group of the DCB map if set to absent | dellos9 |
| ``dcb_map.priority_pgid`` |string (required)       | Configures priority to priority-group mapping; value is the PGID of priority groups separated by a space (1 1 2 2 3 3 3 4) | dellos9 |
| ``dcb_map.intf`` | list           | Configures the DCB map to the interface (see ``intf.*``) | dellos9 |
| ``intf.name`` | string (required)        | Configures the DCB map to the interface with this interface name             | dellos9 |
| ``intf.state`` | string: absent,present\*   | Deletes the DCB map from the interface if set to absent         | dellos9 |
| ``dcb_map.state`` | string: absent,present\*   | Deletes the DCB map if set to absent               |  dellos9 |
| ``dcb_buffer`` | list | Configures the DCB buffer profile (see ``dcb_buffer.*``) | dellos9 |
| ``dcb_buffer.name`` | string (required)         | Configures the DCB buffer profile name                           | dellos9 |
| ``dcb_buffer.description`` | string (required)         | Configures a description about the DCB buffer profile   | dellos9 |
| ``dcb_buffer.priority_params`` | list           | Configures priority flow-control buffer parameters (see ``priority_params.*``)| dellos9 |
| ``priority_params.pgid`` | integer (required): 0-7        | Specifies the priority-group ID                    | dellos9 |
| ``priority_params.buffer_size`` | int (required)         | Configures the ingress buffer size (in KB) of the DCB buffer profile | dellos9 |
| ``priority_params.pause`` | integer          | Configures the buffer limit (in KB) for pausing                           | dellos9 |
| ``priority_params.resume`` | integer          | Configures buffer offset limit (in KB) for resume                        | dellos9 |
| ``priority_params.state`` | string: absent,present\*   | Deletes the priority flow parameters of the DCB buffer if set to absent  | dellos9 |
| ``dcb_buffer.intf`` | list           | Configures the DCB buffer to the interface (see ``intf.*``) | dellos9 |
| ``intf.name`` | string (required)        | Configures the DCB buffer to the interface with this interface name       | dellos9 |
| ``intf.state`` | string: absent,present\*   | Deletes the DCB buffer from the interface if set to absent    | dellos9 |
| ``dcb_buffer.state`` | string: absent,present\*   | Deletes the DCB buffer profile if set to absent         | dellos9 |

> **NOTE**: Asterisk (\*) denotes the default value if none is specified. 

Connection variables
--------------------

Ansible Dell EMC Networking roles require connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible *group_vars* or *host_vars* directories or inventory, or in the playbook itself.

| Key         | Required | Choices    | Description                                         |
|-------------|----------|------------|-----------------------------------------------------|
| ``ansible_host`` | yes      |            | Specifies the hostname or address for connecting to the remote device over the specified transport |
| ``ansible_port`` | no       |            | Specifies the port used to build the connection to the remote device; if value is unspecified, the ANSIBLE_REMOTE_PORT option is used; it defaults to 22 |
| ``ansible_ssh_user`` | no       |            | Specifies the username that authenticates the CLI login for the connection to the remote device; if value is unspecified, the ANSIBLE_REMOTE_USER environment variable value is used  |
| ``ansible_ssh_pass`` | no       |            | Specifies the password that authenticates the connection to the remote device.  |
| ``ansible_become`` | no       | yes, no\*   | Instructs the module to enter privileged mode on the remote device before sending any commands; if value is unspecified, the ANSIBLE_BECOME environment variable value is used, and the device attempts to execute all commands in non-privileged mode |
| ``ansible_become_method`` | no       | enable, sudo\*   | Instructs the module to allow the become method to be specified for handling privilege escalation; if value is unspecified, the ANSIBLE_BECOME_METHOD environment variable value is used. |
| ``ansible_become_pass`` | no       |            | Specifies the password to use if required to enter privileged mode on the remote device; if ``ansible_become`` is set to no this key is not applicable. |
| ``ansible_network_os`` | yes      | dellos6/dellos9/dellos10, null\*  | This value is used to load the correct terminal and cliconf plugins to communicate with the remote device. |

> **NOTE**: Asterisk (\*) denotes the default value if none is specified.

Dependencies
------------

The *dellos-dcb* role is built on modules included in the core Ansible code. These modules were added in Ansible version 2.2.0.

Example playbook
----------------

This example uses the *dellos-dcb* role to completely configure DCB map and DCB buffer profiles and assigns it to interfaces. The example creates a *hosts* file with the switch details and corresponding variables. The hosts file should define the *ansible_network_os*/ *ansible_net_os_name* variable with corresponding Dell EMC networking OS name. 

When *dellos_cfg_generate* is set to true, the variable generates the configuration commands as a .part file in *build_dir* path. By default it is set to false. It writes a simple playbook that only references the *dellos-dcb* role.  

**Sample hosts file**

    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9)>

**Sample host_vars/leaf1**

    hostname: leaf1
    ansible_become: yes
    ansible_become_method: xxxxx
    ansible_become_pass: xxxxx
    ansible_ssh_user: xxxxx
    ansible_ssh_pass: xxxxx
    ansible_network_os: dellos9
    build_dir: ../temp/dellos9
    dellos_dcb:
        dcb_map:
          - name:  test
            priority_pgid: 0 0 0 3 3 3 3 0 
            priority_group:
              - pgid: 0
                bandwidth: 20
                pfc: true
                state: present
              - pgid: 3
                bandwidth: 80
                pfc: true
                state: present
            intf:
              - name: fortyGigE 1/8
                state: present
              - name: fortyGigE 1/9
                state: present
        state: present
        dcb_buffer:
          - name: buffer
            description:
            priority_params:
              - pgid: 0
                buffer_size: 5550
                pause: 40
                resume: 40
                state: present
            intf:
              - name: fortyGigE 1/8
                state: present
        state: present
        
**Simple playbook to setup system - leaf.yaml**

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-dcb

**Run**

    ansible-playbook -i hosts leaf.yaml

(c) 2017 Dell EMC
