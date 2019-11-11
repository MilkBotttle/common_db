# Deploy MaraiaDB
Deploy mariadb cluster or a single node

## Requirement
1. Network already configured. The VIP interface already exist. And
each node can access to each other.
2. Only support on CentOS
3. Download rpm tarball from NAS and extract put in playbook dir.
```
wget -O commondb_rpm.tgz 'http://172.20.0.22:8080/share.cgi?ssid=0XOlXwX&fid=0XOlXwX&ep=LS0tLQ=='
tar zxvf commondb_rpm.tgz
```

## How to deploy single mariadb node
1. Wriete a inventory with vars and host.
> `mysql_address` is mysql bind address

Example:
```
[commondb]
node-master ansible_host=192.168.0.254 mysql_address=192.168.0.254

[commondb:vars]
mariadb_root_password=password
commondb_user_password=password
```
2. Run `ansible-playbook -i inventory site.yaml`

## How to deploy a mariadb cluster (at least 3 nodes)
> Note: The playbook will pick first node in inventory to bootstrap
> database.

1. Write a inventory file with vars and hosts.
> `mysql_address` is mysql bind address

Example:
```
[commondb]
node2 ansible_host=192.168.0.214 mysql_address=192.168.0.214
node1 ansible_host=192.168.0.98 mysql_address=192.168.0.98
node-master ansible_host=192.168.0.254 mysql_address=192.168.0.254

[commondb:vars]
mariadb_root_password=password
commondb_user_password=password
vip=192.168.0.100
vip_interface=eth0
```
And run `ansible-playbook -i inventory site.yaml`

## Destroy deploy
> Note: This will remove installed rpm and clean all database data

Run: `ansible-playbook -i inventory destroy_all.yaml`

## Variables in inventory
* ansible_host: ansible ssh address
* mysql_address: mysql bind address
* vip: keepalived create vip address
* vip_interface: keepalived vip interface
* commondb_user_password: a password for user commondb

## Upgrade MariaDB
1. Prepare a fresh CentOS machine with internal connection
2. Download the newer version MariaDB packages from offcial site
```
curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
mkdir rpm
yum install --downloadonly --downloaddir=$PWD/rpm MariaDB
```
3. Create repo for newer rpm
```
yum install -y yum-utils
createrepo $PWD/rpm
```
4. Copy the rpm folder to ansible playbook directory
5. Run upgrade playbook
```
ansible-playbook -i inventory upgrade.yaml
```
