# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile for Kroeg
# https://github.com/argrath/kroeg-vagrant

# IP Address of VM
$ipaddress = "192.168.1.203"

Vagrant.configure("2") do |config|
  config.vm.box = "debian/contrib-stretch64"
  config.vm.network "public_network", ip: $ipaddress
  config.vm.synced_folder "data", "/vagrant"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
  end

#
$provision = <<SCRIPT
# install dotnet core
export DOTNET_CLI_TELEMETRY_OPTOUT=1
sudo apt-get update
sudo apt-get install -y curl libunwind8 gettext apt-transport-https
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg

sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-debian-stretch-prod stretch main" > /etc/apt/sources.list.d/dotnetdev.list'

sudo apt-get update
sudo apt-get install -y dotnet-sdk-2.0.2

# install postgresql
sudo apt-get install -y postgresql
echo "create database activitypub; alter role postgres with password 'postgres';"| sudo -u postgres psql

# install gti
sudo apt-get install -y git

# clone sources
git clone https://github.com/puckipedia/Kroeg.git

# patch config
perl -pi -e 's/localhost:5000/#{$ipaddress}:5000/g' Kroeg/Kroeg.Server/appsettings.json

# make startup script
echo 'cd Kroeg/Kroeg.Server/; dotnet run' > startup.sh

# build
cd Kroeg/Kroeg.Server
dotnet build
SCRIPT

  config.vm.provision "shell", inline: $provision, privileged: false

end
