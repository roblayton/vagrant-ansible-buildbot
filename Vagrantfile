# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

cluster = {
  "master" => { :ip => "192.168.33.10", :cpus => 1, :mem => 1024 },
  "slave" => { :ip => "192.168.33.11", :cpus => 1, :mem => 1024 }
}
 
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  cluster.each_with_index do |(hostname, info), index|

    config.vm.define hostname do |cfg|
      cfg.vm.provider :virtualbox do |vb, override|
        config.vm.box = "ubuntu/trusty64"
        override.vm.network :private_network, ip: "#{info[:ip]}"
        override.vm.hostname = hostname
        vb.name = hostname
        vb.customize ["modifyvm", :id, "--memory", info[:mem], "--cpus", info[:cpus], "--hwvirtex", "on"]
      end # end provider
    
      # provision on the last node
      if index == cluster.size - 1
        cfg.vm.provision :ansible do |ansible|
          ansible.playbook = "buildbot.yaml"
          ansible.extra_vars = "vars.yaml"
          ansible.inventory_path = "hosts"
          ansible.verbose = "vvvv"
          ansible.host_key_checking = false
          ansible.sudo = true
          ansible.limit = "all"
        end # end provision
      end # end if

    end # end config

  end # end cluster
end
