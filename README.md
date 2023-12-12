# openstack-ansible-rockylinux9
Rocky Linux 9.3, 5.14.0-362.8.1.el9_3.x86_64(Installed from ISO file on to a bare metal server). All in one, Openstact-ansible 27.0.0.0rc1.

Disable SELinux. (https://docs.openstack.org/openstack-ansible/latest/user/aio/quickstart.html)

```
systemctl stop firewalld
systemctl mask firewalld
git clone https://opendev.org/openstack/openstack-ansible   /opt/openstack-ansible
cd /opt/openstack-ansible
git checkout master
```
Now start to install:
```
scripts/bootstrap-ansible.sh
scripts/bootstrap-aio.sh
```

Got error as following:
```
fatal: [localhost]: FAILED! => {"changed": false, "failures": [], "msg": "Depsolve Error occurred: \n Problem:
problem with installed package curl-7.76.1-26.el9_3.2.0.1.x86_64\n - package curl-minimal-7.76.1-26.el9_3.2.0.1.x86_64
from baseos conflicts with curl provided by curl-7.76.1-26.el9_3.2.0.1.x86_64 from @System\n - package curl-minimal-7.76.1-26.el9_3.2.0.1.x86_64
from baseos conflicts with curl provided by curl-7.76.1-26.el9_3.2.0.1.x86_64 from baseos\n - conflicting requests", "rc": 1, "results": []}

```
The curl package has been installed by default, and it is required by dnf package. While bootstrap-aio.sh trying to install curl-minimal,
it failed like above.

Change file **"ests/roles/bootstrap-host/vars/redhat.yml"**, comment the line of **"/usr/bin/curl"**, then it worked fine.

After the installation is done, then we can start to run the ansible [laybook to setup:

Before that, we need to change one file **"/etc/ansible/roles/openstack_hosts/vars/redhat-9.yml"**, which has the same "curl" conflicts issue.
Comment the line of **"curl"**.

**(see the bug report here: https://bugs.launchpad.net/openstack-ansible/+bug/2046172)**

```
openstack-ansible setup-hosts.yml
openstack-ansible setup-infrastructure.yml
openstack-ansible setup-openstack.yml
```

Then it should be done.

