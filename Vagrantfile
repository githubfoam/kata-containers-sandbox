# -*- mode: ruby -*-
# vi: set ft=ruby :
$katacontainers_script = <<SCRIPT
# Install Kata Containers on Ubuntu
# https://github.com/kata-containers/documentation/blob/master/install/ubuntu-installation-guide.md
ARCH=$(arch)
BRANCH="${BRANCH:-master}"
sudo sh -c "echo 'deb http://download.opensuse.org/repositories/home:/katacontainers:/releases:/${ARCH}:/${BRANCH}/xUbuntu_$(lsb_release -rs)/ /' > /etc/apt/sources.list.d/kata-containers.list"
curl -sL  http://download.opensuse.org/repositories/home:/katacontainers:/releases:/${ARCH}:/${BRANCH}/xUbuntu_$(lsb_release -rs)/Release.key | sudo apt-key add -
sudo -E apt-get update
sudo -E apt-get -y install kata-runtime kata-proxy kata-shim
# Install Docker for Kata Containers on Ubuntu
# https://github.com/kata-containers/documentation/blob/master/install/docker/ubuntu-docker-install.md
sudo -E apt-get -y install apt-transport-https ca-certificates software-properties-common
curl -sL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
arch=$(dpkg --print-architecture)
sudo -E add-apt-repository "deb [arch=${arch}] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo -E apt-get update
sudo -E apt-get -y install docker-ce
# Configure Docker to use Kata Containers by default with ONE of the following methods:
sudo mkdir -p /etc/systemd/system/docker.service.d/
cat <<EOF | sudo tee /etc/systemd/system/docker.service.d/kata-containers.conf
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -D --add-runtime kata-runtime=/usr/bin/kata-runtime --default-runtime=kata-runtime
EOF
sudo systemctl daemon-reload && sudo systemctl restart docker
echo "==================================================================="
docker --version
echo "==================================================================="
hostnamectl status
# check whether the new run time is set up
echo "==================================================================="
sudo docker info | grep Runtime
echo "==================================================================="
# Run Kata Containers
# sudo docker run busybox uname -a
SCRIPT
BOX_IMAGE = "bento/ubuntu-19.04"
HOSTNAME = "kata-dev"
Vagrant.configure("2") do |config|
  config.vm.box = BOX_IMAGE
  config.vm.box_check_update = false
  config.vm.hostname = HOSTNAME
  config.vm.network "private_network", ip: "192.168.50.10"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "524"
    vb.cpus = "2"
  end
  config.vm.provision "shell", inline: $katacontainers_script, privileged: false
end
