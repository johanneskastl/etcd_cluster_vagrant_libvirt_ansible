# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  ###################################################################################
  # define number of etcd nodes
  E = 3

  # provision E VMs as nodes
  (1..E).each do |i|

    # name the VMs
    config.vm.define "etcd#{i}" do |node|

      # which image to use
      node.vm.box = "opensuse/Leap-15.3.x86_64"

      # sizing of the VMs
      node.vm.provider "libvirt" do |lv|
        lv.random_hostname = false
        lv.memory = 1024
        lv.cpus = 1
      end

      # set the hostname
      node.vm.hostname = "etcd#{i}"

      # if this is the last machine
      if i == E

        #################################################
        # only target one node to not run ansible twice
        # but do not limit this to this node (set ansible.limit to all)
        node.vm.provision "ansible" do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.groups = {
            "etcd_nodes"  => [ "etcd1", "etcd2", "etcd3" ],
          }
          ansible.playbook = "ansible/playbook-vagrant.yml"

        end # node.vm.provision
      end # if-condition last machine
    end # config.vm.define nodes
  end # each-loop over all VMs
end
