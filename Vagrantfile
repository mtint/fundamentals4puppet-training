# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANT_REQUIRED_LINKED_CLONE_VERSION = "1.8.0"

Vagrant.configure("2") do |config|
  config.vm.define "puppet" do |puppet|
    puppet.vm.hostname = "puppet"
    puppet.vm.host_name = "puppet.localdomain"
    puppet.vm.box = "bento/centos-7.5"
    puppet.vm.box_check_update = true
    puppet.vm.network "private_network", ip: "192.168.56.101"

    puppet.vm.provider "virtualbox" do |vb|
      vb.name = "puppet.localdomain"
      vb.linked_clone = true if Gem::Version.new(Vagrant::VERSION) >= Gem::Version.new(VAGRANT_REQUIRED_LINKED_CLONE_VERSION)
      vb.gui = false
      vb.memory = "1024"
    end

    puppet.vm.provision "shell", inline: <<-SHELL
      rm -rf /var/cache/yum
      rpm --import https://yum.puppet.com/RPM-GPG-KEY-puppet
      rpm -aq |grep puppet5-release 1>/dev/null || yum install -y https://yum.puppet.com/puppet5/puppet5-release-el-7.noarch.rpm
      rpm -q git || yum install -y git
      rpm -q puppet-agent || yum install -y puppet-agent
      useradd -g vagrant -d /home/training -m training 2>/dev/null
      sed -i 's|^training:.*$|training:$6$6kKwWz2c$eVYoDNonqIItGB9hsapr5LX3NkOM4sySMX189ABgAayxXYV1MnoUjt2zAQbvhN9pW/RfsQS8Z/iWGa0nCByRD0:17455:0:99999:7:::|g' /etc/shadow
      install -o training -g vagrant -m700 -d /home/training/.ssh
      echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD3lYc4I617lXh8lAz5k7B/bnH1gceZ85Un50UBP78gvymSVT6q7CBrSDqyH+n0Bso7zHQX5p3BbmFiIuWq5jskJ/6qc53LLuzO3Mi4h2SwEwhXnlnst1bgvkxNwH6rLpd3W+48j+jnYwb0YOIxldZb67MZPUT7bplMwWTMaWKz1i5qIWK2nTmJkSAp5vWFAorsl6fa+DtC8Id3pbt54TUxjA6L7bZ9xYma2SNav0YQsc4WFtCUZz5/uSSRlYQMFO5DwwIjSN4mXGmxHCtI+3WmMDXE1KUJ5S5ifC01qP9Js7YCP0qyMJTai39T++0NYZXjVpWyos9DOnuK9Y6i/Wi7 training@agent" > /home/training/.ssh/authorized_keys; chown training.vagrant /home/training/.ssh/authorized_keys
    SHELL

    puppet.vm.provision :puppet do |puppet|
      puppet.environment_path = "puppet/environments"
      puppet.environment = "production"
    end
  end

  config.vm.define "agent-centos" do |agent|
    agent.vm.hostname = "agent-centos"
    agent.vm.host_name = "agent-centos.localdomain"
    agent.vm.box = "bento/centos-7.5"
    agent.vm.box_check_update = true
    agent.vm.network "private_network", ip: "192.168.56.102"

    agent.vm.provider "virtualbox" do |vb|
      vb.name = "agent-centos.localdomain"
      vb.linked_clone = true if Gem::Version.new(Vagrant::VERSION) >= Gem::Version.new(VAGRANT_REQUIRED_LINKED_CLONE_VERSION)
      vb.gui = false
      vb.memory = "512"
    end

    agent.vm.provision "shell", inline: <<-SHELL
      rpm -q vim || yum install -y vim
      rpm -q git || yum install -y git
    SHELL
    agent.vm.provision :shell, :path => 'scripts/agents.sh', :args => 'git-init'
  end

  config.vm.define "agent-debian" do |agent|
    agent.vm.hostname = "agent-debian"
    agent.vm.host_name = "agent-debian.localdomain"
    agent.vm.box = "bento/debian-9.4"
    agent.vm.box_check_update = true
    agent.vm.network "private_network", ip: "192.168.56.103"

    agent.vm.provider "virtualbox" do |vb|
      vb.linked_clone = true if Gem::Version.new(Vagrant::VERSION) >= Gem::Version.new(VAGRANT_REQUIRED_LINKED_CLONE_VERSION)
      vb.gui = false
      vb.memory = "512"
    end

    agent.vm.provision "shell", inline: <<-SHELL
      apt-get update
      dpkg-query --show vim 1>/dev/null || apt-get -y install vim
      ln -s /usr/bin/vim.tiny /usr/bin/vim
      dpkg-query --show git 1>/dev/null || apt-get -y install git
    SHELL
    agent.vm.provision :shell, :path => 'scripts/agents.sh', :args => 'git-init'
  end

  config.vm.define "agent-debian" do |agent|
    agent.vm.hostname = "agent-debian"
    agent.vm.host_name = "agent-debian.localdomain"
    agent.vm.box = "bento/debian-9.4"
    agent.vm.box_check_update = false
    agent.vm.network "private_network", ip: "192.168.56.103"

    agent.vm.provider "virtualbox" do |vb|
      vb.name = "agent-debian.localdomain"
      vb.linked_clone = true if Gem::Version.new(Vagrant::VERSION) >= Gem::Version.new(VAGRANT_REQUIRED_LINKED_CLONE_VERSION)
      vb.gui = false
      vb.memory = "512"
    end

    agent.vm.provision "shell", inline: <<-SHELL
      if ! $(dpkg-query --show puppet5-release 2>/dev/null); then
        wget https://apt.puppetlabs.com/puppet5-release-stretch.deb 2>/dev/null
        dpkg -i puppet5-release-stretch.deb
        rm -f puppet5-release-stretch.deb
      fi
      apt-get update 2>&1
      dpkg-query --show git 2>/dev/null || apt-get -y install git 2>/dev/null
      dpkg-query --show puppet-agent 2>/dev/null || apt-get -y install puppet-agent 2>/dev/null
    SHELL
    agent.vm.provision :shell, :path => 'scripts/agents.sh'
  end

end
