# ansible-cluster-api-provider-vsphere-setup

This Ansible role will setup a vSphere provider in preparation for using cluster-api.  This is designed to replace and/or supplement steps at https://github.com/kubernetes-sigs/cluster-api-provider-vsphere/blob/master/docs/getting_started.md#vsphere-requirements. Specifically, the following steps occur:

- Download and import supported OVAs into vCenter as virtual machines.
- Snapshot the virtual machines (needed for linked clones).
- Mark the virtual machines as templates.

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
# create the virtualenv
virtualenv -p <PATH_TO_PYTHON_EXE> <MY_VIRTUAL_ENV_NAME>

# activate the virtualenv
source <MY_VIRTUAL_ENV_NAME>/bin/activate
```

Install the requirements, which will subsequently install Ansible as well:
```
# install python module requirements
pip install -r requirements.txt

# install this repository as an ansible role
ansible-galaxy install -r requirements.yml
```

Create your vars file via the spec defined at https://github.com/kubernetes-sigs/cluster-api-provider-vsphere/blob/master/docs/getting_started.md#configuring-and-installing-cluster-api-provider-vsphere-in-a-management-cluster:
```yaml
## CURRENT AS OF 3/20/2020
## -- Controller settings -- ##
VSPHERE_USERNAME: "vi-admin@vsphere.local"                    # The username used to access the remote vSphere endpoint
VSPHERE_PASSWORD: "admin!23"                                  # The password used to access the remote vSphere endpoint

## -- Required workload cluster default settings -- ##
VSPHERE_SERVER: "10.0.0.1"                                    # The vCenter server IP or FQDN
VSPHERE_DATACENTER: "SDDC-Datacenter"                         # The vSphere datacenter to deploy the management cluster on
VSPHERE_DATASTORE: "DefaultDatastore"                         # The vSphere datastore to deploy the management cluster on
VSPHERE_NETWORK: "VM Network"                                 # The VM network to deploy the management cluster on
VSPHERE_RESOURCE_POOL: "*/Resources"                          # The vSphere resource pool for your VMs
VSPHERE_FOLDER: "vm"                                          # The VM folder for your VMs. Set to "" to use the root vSphere folder
VSPHERE_TEMPLATE: "ubuntu-1804-kube-v1.17.3"                  # The VM template to use for your management cluster.
VSPHERE_HAPROXY_TEMPLATE: "capv-haproxy-v0.6.0"  # The VM template to use for the HAProxy load balancer
VSPHERE_SSH_AUTHORIZED_KEY: "ssh-rsa AAAAB3N..."              # The public ssh authorized key on all machines
                                                              #   in this cluster.
                                                              #   Set to "" if you don't want to enable SSH,
                                                              #   or are using another solution.
```

Create a playbook to utilize the role **(example at./site.yml)**:
```
- hosts:        'localhost'
  gather_facts: false
  connection:   'local'
  roles:
    - role: 'ansible-cluster-api-provider-vsphere-setup'
```

Run the playbook:
```
ansible-playbook -vv ./site.yml -e@~/.cluster-api/clusterctl.yaml
```

If you need to override specific variables from the clusterctl.yaml file or the `defaults/main.yml` file, the following is applicable:
```
ansible-playbook -vv site.yml -e @/Users/sdustin/.cluster-api/clusterctl.yaml -e "VSPHERE_DATASTORE=vmw_iscsi_q01iso VSPHERE_FOLDER='/Scott-Home/vm/Templates/Linux' ova_download_directory='/path/to/ovas'"
```
