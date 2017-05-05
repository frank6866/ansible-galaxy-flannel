# flannel

This role is used to deploy flannel. Supporting features as follows:

* install flannel
* configuration for TLS or authenticated etcd cluster

## Prepare
Before you run this role, you should have an etcd environment. For example, the endpoints is "https://10.10.10.11:2379,https://10.10.10.12:2379,https://10.10.10.13:2379".

Adding a key in etcd:

```
# etcdctl set /flannel-test/network/config '{ "Network": "10.1.0.0/16" }'
```

create user:

```
# etcdctl user add flannel
```

create role:

```
# etcdctl role add role-flannel
Role role-flannel created
```

grant role access to the path create above:

```
# etcdctl role grant role-flannel -path '/flannel-test/*' -readwrite
```

add user into role:

```
# etcdctl user grant flannel-test -roles role-flannel
```


## Inventory file demo
If you etcd cluster is not configed with TLS, setting the following variables in inventory file:

```
frank6866-flannel-1 ansible_ssh_host=192.168.168.201 ansible_ssh_port=3322 ansible_ssh_user=centos flannel_public_ip=192.168.168.201
frank6866-flannel-2 ansible_ssh_host=192.168.168.202 ansible_ssh_port=3322 ansible_ssh_user=centos flannel_public_ip=192.168.168.202
frank6866-flannel-3 ansible_ssh_host=192.168.168.203 ansible_ssh_port=3322 ansible_ssh_user=centos flannel_public_ip=192.168.168.203

[flannel]
vagrant[1:3]

[flannel:vars]
flannel_etcd_endpoints=https://10.10.10.11:2379,https://10.10.10.12:2379,https://10.10.10.13:2379
flannel_etcd_prefix=/flannel-test/network
```


If you etcd cluster is configed with TLS and authentication is enabled, setting the following variables in inventory file:

```
frank6866-flannel-1 ansible_ssh_host=192.168.168.201 ansible_ssh_port=3322 ansible_ssh_user=centos flannel_public_ip=192.168.168.201
frank6866-flannel-2 ansible_ssh_host=192.168.168.202 ansible_ssh_port=3322 ansible_ssh_user=centos flannel_public_ip=192.168.168.202
frank6866-flannel-3 ansible_ssh_host=192.168.168.203 ansible_ssh_port=3322 ansible_ssh_user=centos flannel_public_ip=192.168.168.203

[flannel]
vagrant[1:3]

[flannel:vars]
flannel_etcd_endpoints=https://10.10.10.11:2379,https://10.10.10.12:2379,https://10.10.10.13:2379
flannel_etcd_prefix=/flannel-test/network
flannel_etcd_tls_enabled=true
flannel_cert_file_path=/tmp/process_csr/etcd.crt
flannel_key_file_path=/tmp/process_csr/etcd.key
flannel_cacert_file_path=/usr/local/etc/openssl/myCA/cacert.pem
flannel_etcd_username=flannel
flannel_etcd_password=pose
```

## Example Playbook
```
---
- hosts: flannel
  become: true
  roles:
    - frank6866.flannel
```

License
-------

MIT

