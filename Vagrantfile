# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/xenial64"

  # Set up network IP and Port forwarding
  config.vm.network "forwarded_port", guest: 5000, host: 5000, host_ip: "127.0.0.1"
  config.vm.network "private_network", ip: "192.168.33.10"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  config.vm.provider "virtualbox" do |vb|
    # Customize the amount of memory and cpus on the VM:
    vb.memory = "512"
    vb.cpus = 1
  end

  # Copy your .gitconfig file so that your git credentials are correct
  if File.exists?(File.expand_path("~/.gitconfig"))
    config.vm.provision "file", source: "~/.gitconfig", destination: "~/.gitconfig"
  end

  # Copy your ssh keys for github so that your git credentials work
  if File.exists?(File.expand_path("~/.ssh/id_rsa"))
    config.vm.provision "file", source: "~/.ssh/id_rsa", destination: "~/.ssh/id_rsa"
  end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available.
  config.vm.provision "shell", inline: <<-SHELL
    curl -fsSL https://clis.ng.bluemix.net/install/linux | sh
    apt-get update
    apt-get install -y git python3 python3-pip mysql-client-core-5.7
    pip3 install -U pip PyMySQL
    apt-get -y autoremove
    # Install PhantomJS for Selenium browser support
     echo "\n***********************************"
     echo " Installing PhantomJS for Selenium"
     echo "***********************************\n"
     sudo apt-get install -y chrpath libssl-dev libxft-dev
     # PhantomJS https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-2.1.1-linux-x86_64.tar.bz2
     cd ~
     export PHANTOM_JS="phantomjs-2.1.1-linux-x86_64"
     wget https://bitbucket.org/ariya/phantomjs/downloads/$PHANTOM_JS.tar.bz2
     sudo tar xvjf $PHANTOM_JS.tar.bz2
     sudo mv $PHANTOM_JS /usr/local/share
     sudo ln -sf /usr/local/share/$PHANTOM_JS/bin/phantomjs /usr/local/bin
     rm -f $PHANTOM_JS.tar.bz2
    # Install app dependencies
    cd /vagrant
    sudo pip install -r requirements.txt
  SHELL

  ######################################################################
  # Add MySQL docker container
  ######################################################################
  config.vm.provision "shell", inline: <<-SHELL
    # Prepare MySQL data share
    sudo mkdir -p /var/lib/mysql
    sudo chown ubuntu:ubuntu /var/lib/mysql
  SHELL

  # Add MySQL docker container
  config.vm.provision "docker" do |d|
    d.pull_images "mysql"
    d.run "mysql",
      args: "--restart=always -d --name mysql -p 3306:3306 -v /var/lib/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=shopcarts"
  end
end
