# Raspberry Pi Ansible for k3s

This is some Ansible I used to provision and install k3s in a 3-node cluster on my Raspberry Pi 3+'s. This
is messy and does some things I would _not_ recommend in a production environment like disabling selinux.
That said, maybe someone else will find these configs useful!

## Dependencies

* An Ansible master machine
* At least one Raspberry Pi running CentOS 7
* A user in the `wheel` group on each host named `ca`
* `wheel` is set to `NOPASSWD ALL` in `/etc/sudoers`
* All of your servers added to your ansible `hosts` file
* Key authentication for the `ca` user on each node (I'd recommend completely disabling password auth)

CentOS on the pi has a root user login open via SSH -- make sure to disable this before opening any port to
the internet.

## Usage

```sh
# Test that your servers are available
ansible-playbook servers-up.yaml

# Install base packages, disable selinux, and enable cgroups in the kernel command line
ansible-playbook default-provision.yaml

# Make sure to reboot your servers for selinux changes to take effect!

# Enable default services
ansible-playbook enable-services.yaml

# Pull k3s onto the servers, enable systemd units, and fire the nodes up!
ansible-playbook k3s-install.yaml
```

Assuming this worked successfully, change the host in `kubecfg` and you should have cluster access!

```sh
KUBECONFIG=./kubecfg kubectl get nodes
```

## Issues

* `--docker` does not work since `k3s` uses a different cgroups driver than `docker` under systemd
* Currently, selinux support is expiremental and I didn't have enought time to dedicate to working
  around it.
