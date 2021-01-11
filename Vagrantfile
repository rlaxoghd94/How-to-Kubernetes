Vagrant.configure("2") do |config|
  config.vm.define "node" do |node|
    node.vm.box = 'ubuntu/bionic64'
    node.vm.hostname = 'node'
    node.vm.network :public_network, ip: '192.168.10.10'
    node.vm.provider "virtualbox" do |v|
            v.memory = 1024
    end
  end
end
