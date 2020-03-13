# ansible-cluster-api-provider-vsphere-setup

This Ansible role will setup a vSphere provider in preparation for using cluster-api.  Specifically, the following steps occur:

- Download and import supported OVAs into vCenter.

## Requirements

- Sufficient space in the `ova_download_directory` (default `$HOME`)
- Python and Pip installed

## Usage

Clone this repository and enter the project directory:

```
git clone https://github.com/scottd018/ansible-cluster-api-provider-vsphere-setup.git
cd ansible-cluster-api-provider-vsphere-setup
```

Create a virtualenv to work in and activate the virtual environment:

```
virtualenv -p <PATH_TO_PYTHON_EXE> <MY_VIRTUAL_ENV_NAME>
source <MY_VIRTUAL_ENV_NAME>/bin/activate
```

Install the requirements:
```
pip install -r requirements.txt
ansible-galaxy install -r requirements.yml
```

Create your vars file (all variables located at `defaults/main.yml`, examples located in examples directory) at **examples/local.yml**:
```
ova_download_directory: "${HOME}"
vcenter_hostname:       'vcenter.example.com'
vcenter_port:           443
vcenter_username:       'administrator@vsphere.local
vcenter_password:       'password'
vcenter_datacenter:     'ha-datacenter'
vcenter_cluster:        'ha-cluster'
vcenter_datastore:      'datastore01'
vcenter_folder:         'vm'
vcenter_networks:
  'nic0': 'VM Network'
```

Create a playbook to utilize the role at **./site.yml**:
```
- hosts:        'localhost'
  gather_facts: false
  connection:   'local'
  roles:
    - role: 'ansible-cluster-api-provider-vsphere-setup'
```

Run the playbook:
```
ansible-playbook -vv ./site.yml -e @examples/local.yml
```