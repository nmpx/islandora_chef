# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.hostname = "islandora-berkshelf"

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "precise64-cloud"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/precise/current/precise-server-cloudimg-amd64-vagrant-disk1.box"

  # Forward port mappings
  config.vm.network :forwarded_port, guest: 8080, host: 8080    # Tomcat
  config.vm.network :forwarded_port, guest: 3306, host: 3306    # MySQL
  config.vm.network :forwarded_port, guest: 5432, host: 5432    # PostgreSQL
  config.vm.network :forwarded_port, guest: 80,   host: 8181    # Apache

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", '3072']
    vb.customize ["modifyvm", :id, "--cpus", "2"]
  end

  # Enabling the Berkshelf plugin.
  config.berkshelf.enabled = true

  # Ensure we are using the latest version of Chef on the VM
  config.omnibus.chef_version = :latest

  config.vm.provision :chef_solo do |chef|
    # Log the heck out of everything
    chef.log_level = :debug
    chef.formatter = :doc

    chef.json = {
=begin
      # TEMPORARY to build the Sandbox / release VM
      "drupal" => {
        "site" => {
          "admin" => "admin",
          "pass" => "islandora",
          "name" => "Islandora Sandbox",
        },
        "db" => {
          "password" => 'islandora'
        }
      },
=end            
      # FIXME: this has to be here due to an override problem; see: http://serverfault.com/questions/541155/
      "java" => {
        "jdk_version" => "7",
        
        # Djatoka requires the Oracle JDK
        "install_flavor" => "oracle",
        "oracle" => {
          "accept_oracle_download_terms" => true
        }
      },

      "tomcat" => {
        "java_options" => "-Xmx1024M -Djava.awt.headless=true -XX:MaxPermSize=128m"
      }, 
    }

    chef.roles_path = "roles"
    
    # NB: order matters here
    chef.add_recipe("ubuntu-baseline")
    chef.add_role("backend")
    chef.add_role("frontend")
  end
end