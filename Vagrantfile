# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"

  config.vm.provision "ansible" do |ansible|
    #ansible.verbose = "vvv"
    ansible.playbook = "site.yaml"
    ansible.become = "true"
  end

  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    v.cpus = 4
  end

  config.vm.define "webserver" do |web|
    web.vm.network "private_network", ip: "192.168.50.10", virtualbox__intnet: "net1"
    web.vm.network "forwarded_port", guest:8080, host:8080
    web.vm.hostname = "webserver"
  end
  config.vm.define "log" do |log|
    log.vm.network "private_network", ip: "192.168.50.15", virtualbox__intnet: "net1"
   #web.vm.network "forwarded_port", guest:8080, host:8080
    log.vm.hostname = "log"
  end
  config.vm.define "elk" do |elk|
    elk.vm.network "private_network", ip: "192.168.50.20", virtualbox__intnet: "net1"
    elk.vm.network "forwarded_port", guest:9200, host:9200
    elk.vm.network "forwarded_port", guest:5601, host:5601
    elk.vm.network "forwarded_port", guest:5000, host:5000
    elk.vm.hostname = "elk"
  end

end
