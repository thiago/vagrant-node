# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "lucid64"
  config.vm.box_url = "http://files.vagrantup.com/lucid64.box"

  #config.vm.provision :shell, :path => "VagrantInstall.sh"

  # Config to symlinks in NPM
  config.vm.provider :virtualbox do |vb|
    vb.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
  end

  # copy .gitconfig (if any) to don't need to reconfigure the VM:
  config.vm.provision "file", source: "~/.gitconfig", destination: "/home/vagrant/.gitconfig"

  # Mapping ports 9000-9003 with AutoCorrect case is already being used
  for i in 9000..9003
    config.vm.network "forwarded_port", guest: i, host: i, auto_correct: true
  end

  # fix bug "stdin: is not a tty"
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"
  # install core deps
  config.vm.provision "shell", inline: "apt-get update"
  config.vm.provision "shell", inline: "apt-get install -y build-essential python-software-properties nfs-common portmap curl"

  # install chef
  config.vm.provision "shell", inline: "gem install chef --version 11.12.2 --no-rdoc --no-ri --conservative"

  # install compass
  config.vm.provision "shell", inline: "gem install compass"

  # import cookiebooks
  config.vm.provision "chef_solo" do |chef|
    chef.add_recipe "nodejs"
    chef.add_recipe "git"
  end

  # fix permission npm
  config.vm.provision "shell", inline: "su - vagrant -c 'echo prefix = ~/.node >> ~/.npmrc'"
  config.vm.provision "shell", inline: "su - vagrant -c 'echo export PATH=$HOME/.node/bin:$PATH >> ~/.bashrc'"
  config.vm.provision "shell", inline: "su - vagrant -c 'echo export NODE_PATH=$NODE_PATH:/home/vagrant/.node/lib/node_modules >> ~/.bashrc && source ~/.bashrc'"

  # intall deps global NPM
  config.vm.provision "shell", inline: "su - vagrant -c 'npm install -g yo'"

  # intall deps local NPM and Bower
  config.vm.provision "shell", inline: "su - vagrant -c 'cd /vagrant && npm install'"
  config.vm.provision "shell", inline: "su - vagrant -c 'cd /vagrant && ~/.node/bin/bower install --config.interactive=false'"

end
