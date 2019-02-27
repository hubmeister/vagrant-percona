# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  #config.vm.box = "damianlewis/ubuntu-18.04-mysql"
  config.vm.box = "ubuntu/xenial64"
  config.vm.hostname = "mysql.example.com"

  config.vm.network "private_network", ip: "192.168.33.248", libvirt__forward_mode: "route", libvirt__dhcp_enabled: false

  config.vm.provider "libvirt" do |lv, config|
    lv.memory = 2048
    lv.cpus = 2
    lv.cpu_mode = "host-passthrough"
    lv.keymap = "pt"
    config.vm.synced_folder ".", "/vagrant", type: "nfs"
  end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update -q
    apt-get install mysql-common
    apt-get install libjemalloc1
    apt-get install libaio1
    apt-get install libmecab2
    apt-get install zlib1g-dev
    apt-get install python -y
    apt-get install debsums -y
    apt-get install libcurl3 -y
    wget https://www.percona.com/downloads/Percona-Server-5.7/Percona-Server-5.7.21-20/binary/debian/xenial/x86_64/Percona-Server-5.7.21-20-red217b06ca3-xenial-x86_64-bundle.tar
    tar xvf Percona-Server-5.7.21-20-red217b06ca3-xenial-x86_64-bundle.tar
    dpkg -i percona-server-common-5.7_5.7.21-20-1.xenial_amd64.deb
    dpkg -i libperconaserverclient20_5.7.21-20-1.xenial_amd64.deb
    dpkg -i libperconaserverclient20-dev_5.7.21-20-1.xenial_amd64.deb
    dpkg -i percona-server-client-5.7_5.7.21-20-1.xenial_amd64.deb
    # (prevents prompts for root password on the server package installation)
    echo percona-server-server-5.7 percona-server-server/root_password password secret | debconf-set-selections
    echo percona-server-server-5.7 percona-server-server/root_password_again password secret | debconf-set-selections
    dpkg -i percona-server-server-5.7_5.7.21-20-1.xenial_amd64.deb
    dpkg -i percona-server-5.7-dbg_5.7.21-20-1.xenial_amd64.deb
    dpkg -i percona-server-rocksdb-5.7_5.7.21-20-1.xenial_amd64.deb
    dpkg -i percona-server-source-5.7_5.7.21-20-1.xenial_amd64.deb
    dpkg -i percona-server-test-5.7_5.7.21-20-1.xenial_amd64.deb
    dpkg -i percona-server-tokudb-5.7_5.7.21-20-1.xenial_amd64.deb
    systemctl enable mysql.service
    systemctl start mysql.service
    wget https://github.com/prometheus/node_exporter/releases/download/v0.17.0/node_exporter-0.17.0.linux-amd64.tar.gz
    wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.10.0/mysqld_exporter-0.10.0.linux-amd64.tar.gz
    mkdir /opt/prometheus_exporters
    tar zxf node_exporter-0.17.0.linux-amd64.tar.gz -C /opt/prometheus_exporters
    tar zxf mysqld_exporter-0.10.0.linux-amd64.tar.gz -C /opt/prometheus_exporters
    cp /opt/prometheus_exporters/node_exporter-0.17.0.linux-amd64/node_exporter /opt/prometheus_exporters
    cp /vagrant/node_exporter.service /etc/systemd/system/node_exporter.service
    cp /opt/prometheus_exporters/mysqld_exporter-0.10.0.linux-amd64/mysqld_exporter /opt/prometheus_exporters
    cp /vagrant/mysqld_exporter.service /etc/systemd/system/mysqld_exporter.service
    cp /vagrant/monitor.ini /etc/mysql/monitor.ini
    mkdir /etc/node_exporter
    groupadd -g 2007 prometheus
    useradd -u 2007 -g 2007 -s /bin/false -c "Prometheus User" prometheus
    systemctl enable node_exporter.service
    systemctl enable mysqld_exporter.service
    systemctl start node_exporter.service
    systemctl start mysqld_exporter.service
   SHELL
end
