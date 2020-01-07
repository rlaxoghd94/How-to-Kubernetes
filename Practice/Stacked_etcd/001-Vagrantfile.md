# Vagrantfile Setup

```Vagrantfile
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
        config.vm.define "loadbalancer" do |loadbalancer|
                loadbalancer.vm.box = 'ubuntu/bionic64'
                loadbalancer.vm.hostname = 'loadbalancer'
                loadbalancer.vm.network :private_network, ip: "192.168.10.10"
                loadbalancer.vm.network "forwarded_port", guest: 80, host: 3000
                loadbalancer.vm.network "forwarded_port", guest: 8404, host: 8404
                
                loadbalancer.vm.provider "virtualbox" do |v|
                        v.memory = 1024
                end
        end

        (1..3).each do |i|
                config.vm.define "node-#{i}" do |node|
                        node.vm.box = 'ubuntu/bionic64'
                        node.vm.hostname = "node-#{i}"
                        node.vm.network :private_network, ip: "192.168.10.#{10+i}"
                        
                        node.vm.provider "virtualbox" do |v|
                                v.memory = 1024
                        end
                end
        end

        config.vm.define "worker" do |worker|
                worker.vm.box = 'ubuntu/bionic64'
                worker.vm.hostname = 'worker'
                worker.vm.network :private_network, ip: '192.168.10.14'

                worker.vm.provider "virtualbox" do |v|
                        v.memory = 1024 
                end
        end
end
```