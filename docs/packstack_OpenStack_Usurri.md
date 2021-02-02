
## Controller node

```
hostnamectl set-hostname ctl01

echo "Setup IP eth0"
nmcli con modify eth0 ipv4.addresses 192.168.98.222/24
nmcli con modify eth0 ipv4.method manual
nmcli con modify eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli con modify eth1 ipv4.addresses 192.168.90.222/24
nmcli con modify eth1 ipv4.gateway 192.168.90.1
nmcli con modify eth1 ipv4.dns 8.8.8.8
nmcli con modify eth1 ipv4.method manual
nmcli con modify eth1 connection.autoconnect yes

sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
sudo systemctl start network

sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

echo "127.0.0.1   `hostname` localhost" > /etc/hosts
echo "192.168.90.222   ctl01 " >> /etc/hosts
echo "192.168.90.223   net01" >> /etc/hosts
echo "192.168.90.224   com01" >> /etc/hosts

init 6
```


## NETWORK NODE 

```
hostnamectl set-hostname net01

echo "Setup IP eth0"
nmcli con modify eth0 ipv4.addresses 192.168.98.223/24
nmcli con modify eth0 ipv4.method manual
nmcli con modify eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli con modify eth1 ipv4.addresses 192.168.90.223/24
nmcli con modify eth1 ipv4.gateway 192.168.90.1
nmcli con modify eth1 ipv4.dns 8.8.8.8
nmcli con modify eth1 ipv4.method manual
nmcli con modify eth1 connection.autoconnect yes

sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
sudo systemctl start network

sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

echo "127.0.0.1   `hostname` localhost" > /etc/hosts
echo "192.168.90.222   ctl01" >> /etc/hosts
echo "192.168.90.223   net01" >> /etc/hosts
echo "192.168.90.224   com01" >> /etc/hosts

init 6
```

## COM1

```
hostnamectl set-hostname com01

echo "Setup IP eth0"
nmcli con modify eth0 ipv4.addresses 192.168.98.224/24
nmcli con modify eth0 ipv4.method manual
nmcli con modify eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli con modify eth1 ipv4.addresses 192.168.90.224/24
nmcli con modify eth1 ipv4.gateway 192.168.90.1
nmcli con modify eth1 ipv4.dns 8.8.8.8
nmcli con modify eth1 ipv4.method manual
nmcli con modify eth1 connection.autoconnect yes

sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
sudo systemctl start network

sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

echo "127.0.0.1   `hostname` localhost" > /etc/hosts
echo "192.168.90.222   ctl01" >> /etc/hosts
echo "192.168.90.223   net01" >> /etc/hosts
echo "192.168.90.224   com01" >> /etc/hosts

init 6
```

Cài đặt repo, package cho tất cả các node.

```
yum install -y https://www.rdoproject.org/repos/rdo-release.rpm
yum install -y centos-release-openstack-train
yum update -y

yum install -y wget crudini  byobu
yum install -y git python-setuptools
yum install -y openstack-packstack
```


Tạo file trả lời để cài đặt packstack

```
packstack --gen-answer-file=/root/rdotraloi.txt \
  --allinone \
  --default-password=Welcome123 \
  --os-cinder-install=y \
  --os-ceilometer-install=n \
  --os-manila-install=y \
  --os-trove-install=n \
  --os-ironic-install=n \
  --os-swift-install=n \
  --os-panko-install=n \
  --os-heat-install=n \
  --os-magnum-install=n \
  --os-aodh-install=n \
  --os-neutron-ovs-bridge-mappings=extnet:br-ex \
  --os-neutron-ovs-bridge-interfaces=br-ex:eth0 \
  --os-neutron-ovs-bridges-compute=br-ex \
  --os-neutron-l2-agent=openvswitch \
  --os-neutron-ml2-type-drivers=vxlan,flat \
  --os-neutron-ml2-tenant-network-types=vxlan \
  --os-controller-host=192.168.90.222 \
  --os-network-hosts=192.168.90.223 \
	--os-compute-hosts=192.168.90.224 \
  --provision-demo=n
```