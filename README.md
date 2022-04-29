# etcd_cluster_vagrant_libvirt_ansible

This Vagrant setup creates three VMs and deploys etcd via ansible. It automatically creates a three node cluster (unless you tell it not to, see below).

Default OS is openSUSE Leap 15.3, but that can be changed in the Vagrantfile. Please beware, this might break the ansible provisioning which was only tested with openSUSE Leap 15.3.

## Vagrant

1. You need vagrant obviously. And ansible. And git...
2. Fetch the box, per default this is `opensuse/Leap-15.3.x86_64`, using `vagrant box add opensuse/Leap-15.3.x86_64`.
3. Make sure the git submodules are fully working by issuing `git submodule init && git submodule update`
4. Run `vagrant up`
5. Log into one of the nodes (e.g. by using `vagrant ssh etcd1`) and run the following command: `etcdctl --endpoints=127.0.0.1:2379 member list`. You should see a list of three members:
```
$ etcdctl --endpoints=127.0.0.1:2379 member list
24c38235bb0b38db, started, etcd3, http://192.168.1.3:2380, http://192.168.1.3V:2379, false
2c600da2657ed1a7, started, etcd1, http://192.168.1.1:2380, http://192.168.1.1:2379, false
dcee767b6b934a4a, started, etcd2, http://192.168.1.2:2380, http://192.168.1.2:2379, false
```

or if you set `ETCDCTL_API=2`:

```
$ etcdctl --endpoints=127.0.0.1:2379 member list
24c38235bb0b38db: name=etcd3 peerURLs=http://192.168.121.3:2380 clientURLs=http://192.168.121.3:2379 isLeader=true
2c600da2657ed1a7: name=etcd1 peerURLs=http://192.168.121.1:2380 clientURLs=http://192.168.121.1:2379 isLeader=false
dcee767b6b934a4a: name=etcd2 peerURLs=http://192.168.121.2:2380 clientURLs=http://192.168.121.2:2379 isLeader=false
```
6. Party!

## Disabling the Ansible provisioning

In case you do not want Ansible to install etcd (because you want to install it yourself), just comment out the following lines in the `Vagrantfile`:
```
        node.vm.provision "ansible" do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.groups = {
            "etcd_nodes"  => [ "etcd1", "etcd2", "etcd3" ],
          }
          ansible.playbook = "ansible/playbook-vagrant.yml"

        end # node.vm.provision
```
