
Vagrant.configure("2") do |config|

  config.vm.box = "generic/fedora27"
  config.vm.synced_folder "host", "/home/vagrant/host"
  config.vm.network "public_network"
  config.vm.provision "shell", inline: "host/setup-host"

  config.vm.provider "virtualbox" do |virtualbox, override|
    virtualbox.memory = 4096
    virtualbox.cpus = 4
  end

end
