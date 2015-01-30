# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.host_name = "djangocore"

  config.vm.provider "virtualbox" do |vb|
      vb.customize ['modifyvm', :id, '--memory', '2048']
  end

  config.ssh.forward_agent = true

  # Shared folders
  hosthome = "#{ENV['HOME']}/"
  config.vm.synced_folder ".", "/djangocore-box", type: "nfs"
  config.vm.synced_folder "../django", "/django", type: "nfs"
  config.vm.synced_folder hosthome, "/home/vagrant/.hosthome"

  # Host-only network required to use NFS shared folders
  config.vm.network "private_network", ip: "1.2.3.4"

  # Provisioning -------------------------------------------------------------

  config.vm.provision :shell, :inline => "/djangocore-box/provisioning/shell/install-chef.sh 11.8.2"
  config.vm.provision :shell, :inline => "su vagrant -c /djangocore-box/provisioning/shell/init-system.sh"

  config.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = "provisioning/chef"
      chef.log_level = :debug
      chef.add_recipe "git"
      chef.add_recipe "openssl"
      chef.add_recipe "memcached"
      chef.add_recipe "mysql::client"
      chef.add_recipe "mysql::server"
      chef.add_recipe "postgresql"
      chef.add_recipe "postgresql::client"
      chef.add_recipe "postgresql::server"
      chef.json.merge!({
          :mysql => {
              :server_root_password => 'secret',
              :server_repl_password => 'secret',
              :server_debian_password => 'secret',
              :bind_address => '127.0.0.1'
          },
          :postgresql => {
              :password => { :postgres => 'secret' },
              :listen_addresses => '*'
          }})
  end

  #config.vm.provision :shell, :inline => "su vagrant -c '/djangocore-box/provisioning/shell/install-python.sh 2.6 2.7 3.3 3.4'"
  #config.vm.provision :shell, :inline => "su vagrant -c /djangocore-box/provisioning/shell/install-gis.sh"
  #config.vm.provision :shell, :inline => "su vagrant -c '/djangocore-box/provisioning/shell/install-pysqlite.sh 2.6 2.7 3.3 3.4'"  # pysqlite isn't Python 3 compatible
  #config.vm.provision :shell, :inline => "su vagrant -c /djangocore-box/provisioning/shell/create-databases.sh"
  #config.vm.provision :shell, :inline => "su vagrant -c /djangocore-box/provisioning/shell/virtual-display.sh"
end
