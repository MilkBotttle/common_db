# Deploy MaraiaDB
Deploy mariadb cluster or a single node

## Requirement
1. Network already configured. The VIP interface already exist. And
each node can access to each other.
2. Only support on CentOS

## How to deploy a mariadb node 
Wriete a inventory with vars and host. Example:
```
[commondb]
node-master ansible_host=192.168.0.254 mysql_address=192.168.0.254

[commondb:vars]
mariadb_root_password=password
```
And run `ansible-playbook -i inventory site.yaml`

## How to deploy a mariadb cluster
Write a inventory file with vars and hosts. Example:
```
[commondb]
node2 ansible_host=192.168.0.214 mysql_address=192.168.0.214
node1 ansible_host=192.168.0.98 mysql_address=192.168.0.98 
node-master ansible_host=192.168.0.254 mysql_address=192.168.0.254

[commondb:vars]
mariadb_root_password=password
vip=192.168.0.100
vip_interface=eth0
```
And run `ansible-playbook -i inventory site.yaml`
## Destroy deploy 
> Note: This will remove installed rpm and clean all database database 
Run: `ansible-playbook -i inventory destroy_all.yaml`

## Variables in inventory 
* ansible_host: ansible ssh address
* mysql_address: mysql bind address 
* vip: keepalived vip 
* vip_interface: keepalived vip interface

