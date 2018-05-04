# Kubernetes Lab

Deploys a Kubernetes Lab on the Rackspace Public Cloud.

## Setup

There is no need to clone this repo. All necessary resources can be referred to over HTTP.

### Install Requirements

Install the OpenStack and Heat clients. An example of installing these into a virtual environment is provided below.

#### Ubuntu 16.04

```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install python-virtualenv
$ mkdir ~/lab-kubernetes/
$ cd ~/lab-kubernetes/
$ virtualenv .venv
$ source .venv/bin/activate
(.venv) $ pip install -r https://raw.githubusercontent.com/cloud-training/lab-kubernetes/master/requirements.txt
(.venv) $ which openstack
```

#### Mac

```
$ brew update
$ brew upgrade
$ brew install python@2
$ pip install virtualenv
$ mkdir ~/lab-kubernetes/
$ cd ~/lab-kubernetes/
$ virtualenv .venv
$ source .venv/bin/activate
(.venv) $ pip install -r https://raw.githubusercontent.com/cloud-training/lab-kubernetes/master/requirements.txt
(.venv) $ which openstack
```

### Credentials

You will need to create a `~/.config/openstack/clouds.yaml` file containing your Rackspace Public Cloud credentials.

```
$ mkdir -p ~/.config/openstack/
$ cat > ~/.config/openstack/clouds.yaml << EOF
clouds:
  my-cloud-account:
    profile: rackspace
    auth:
      project_id: ENTER_YOUR_CLOUD_ACCOUNT_PROJECT_ID_HERE
      username: ENTER_YOUR_CLOUD_ACCOUNT_USERNAME_HERE
      password: ENTER_YOUR_CLOUD_ACCOUNT_PASSWORD_HERE
    regions:
      - DFW
      - ORD
      - IAD
      - SYD
      - HKG
    interface: public
EOF

$ chmod 600 ~/.config/openstack/clouds.yaml
```

## Process

### Create Lab

If you created a virtual environment above, ensure you have activated that virtual environment, and that the OpenStack and Heat clients are installed. Create your Kubernetes Lab by running the following command. This will generate a Heat (Orchestration) Stack containing all the necessary resources for your Kubernes Lab, bootstrap the Master Node, and join any Worker Nodes if specified.

```
(.venv) $ openstack stack create                                                                    \
  --os-cloud my-cloud-account                                                                       \
  --os-region ORD                                                                                   \
  --parameter worker_node_count=3                                                                   \
  --template https://raw.githubusercontent.com/cloud-training/lab-kubernetes/master/heat/stack.yaml \
  my-stack-name
```

#### Credentials

The `--os-cloud` option should correspond to the cloud name you defined in your `clouds.yaml` file. Our example uses a cloud named `my-cloud-account`.

The `--os-region` option specifies the region to deploy the Stack into. If ommitted, the first region defined in the `clouds.yaml` for the specified `--os-cloud` will be used. Our exmple specifies the `ORD` region, but if we ommitted it in our example, then the `DFW` region would be used by default.

#### Parameters

The `worker_node_count` parameter allows specifying the number of Kubernetes Worker Nodes you would like deployed. If ommitted, it defaults to `worker_node_count=2`. If `worker_node_count=0` is specified, then no Kubernetes Worker Nodes will be deployed, and the Kubernetes Master Node will be untained to allow scheduling of Pods to itself.

The `lab_repo` parameter allows specifying an alternative repo to clone from, perhaps a fork you maintain or are testing changes in. If ommitted, it defaults to this (`https://github.com/cloud-training/lab-kubernetes.git`) repo.

The `lab_repo_version` parameter allows specifying an alternative version to checkout, such as a specific tag or branch. If ommitted, it defaults to the `master` branch.
