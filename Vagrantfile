# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.provision "file", source: "./files/vagrant_test.pub", destination: "/home/vagrant/.ssh/"

  config.vm.define "driver" do |driver|
    driver.vm.box = "ubuntu/focal64"
    driver.vm.hostname = "driver"
    driver.vm.network "private_network", ip: "192.168.51.2"
    driver.vm.synced_folder "./ansible","/home/vagrant/ansible"
    driver.vm.synced_folder "./files","/home/vagrant/files/"
    driver.vm.provision "file", source: "files/vagrant_test", destination: "/home/vagrant/.ssh/"
    driver.vm.provision "shell", inline: <<-SHELL
      sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/#g' /etc/ssh/sshd_config
      service ssh restart
      sudo add-apt-repository ppa:ansible/ansible
      sudo apt update -y && sudo apt -y install sshpass ansible
      chmod 600 /home/vagrant/.ssh/vagrant_test
      chmod 644 /home/vagrant/.ssh/vagrant_test.pub
    SHELL
  end

  config.vm.define "worker" do |worker|
    worker.vm.box = "bento/centos-7.9"
    worker.vm.hostname = "worker"
    worker.vbguest.auto_update = false
    worker.vm.network "private_network", ip: "192.168.51.3"
    worker.vm.provision "shell", inline: <<-SHELL
      chmod 644 /home/vagrant/.ssh/vagrant_test.pub
      cat /home/vagrant/.ssh/vagrant_test.pub >> /home/vagrant/.ssh/authorized_keys
      curl -sL https://rpm.nodesource.com/setup_10.x | sudo bash -
      sudo yum update -y update
    SHELL
  end
end
