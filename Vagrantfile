# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "precise32"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "http://files.vagrantup.com/precise32.box"
  # Adding to attempt to debug issues associated with upgrading to Yosemite and having to redownload and install the VBOX
  Vagrant.configure("2") do |config|
   config.ssh.private_key_path = "~/.ssh/id_rsa"
   config.ssh.forward_agent = true
  end

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine.
  # Forward MySql port on 33066, used for connecting admin-clients to localhost:33066
  config.vm.network :forwarded_port, guest: 3306, host: 33066
  # Forward http port on 8080, used for connecting web browsers to localhost:8080
  config.vm.network :forwarded_port, guest: 80, host: 8080
  # Configure timeout, because having issues after upgrading to Yosemite and downloading 
  # a fresh Virtual Box
  config.vm.boot_timeout = 600
  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network :private_network, ip: "192.168.33.10"
  #Change vm name to your project name.
  vm_name = "vgt-lamp"
  # Set share folder permissions to 777 so that apache can write files
#  config.vm.synced_folder ".", "/vagrant", mount_options: ['dmode=777','fmode=666']
   config.vm.synced_folder ".", "/vagrant", :nfs => true
  # Provider-specific configuration so you can fine-tune VirtualBox for Vagrant.
  # These expose provider-specific options.
  config.vm.provider :virtualbox do |vb|
    # Use VBoxManage to customize the VM. For example to change memory:
 #   vb.customize ["modifyvm", :id, "--memory", "512"]
 #   vb.customize ["modifyvm", :id, "--memory", "1024"]
     vb.customize ["modifyvm", :id, "--memory", "4000"]
 
  end

  # Enable provisioning with chef solo, specifying a cookbooks path, roles
  # path, and data_bags path (all relative to this Vagrantfile), and adding
  # some recipes and/or roles.
  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = "cookbooks"
    chef.data_bags_path = "data_bags"

    # List of recipes to run
    chef.add_recipe "vagrant_main"
    chef.add_recipe "vagrant_main::wordpress"
    chef.add_recipe "vagrant_main::drupal"
    chef.add_recipe "vagrant_main::nodejs"
  end
end
