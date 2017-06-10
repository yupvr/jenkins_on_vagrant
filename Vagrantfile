# -*- mode: ruby -*-
# vi: set ft=ruby :

$install_tools = <<SCRIPT
set -x
yum upgrade -y
yum install -y git
SCRIPT


Vagrant.configure('2') do |config|

  config.vm.synced_folder '.', '/home/vagrant/sync', disabled: true
  config.vm.synced_folder '.', '/vagrant', disabled: true
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true

  config.vm.define 'jenkins-server' do |jenkins|
    jenkins.vm.provider "docker" do |d|
      d.image = "jenkins"
    end
    jenkins.vm.hostname = 'jenkins-server'
    jenkins.vm.synced_folder './jenkins', '/var/jenkins_home', create: true
    jenkins.vm.network 'forwarded_port', guest: 8080, host: 8080
    jenkins.vm.network 'forwarded_port', guest: 50000, host: 50000
  end

  config.vm.define 'jenkins-slave' do |slave|
    slave.vm.provider 'libvirt'
    slave.vm.box = 'centos/7'
    slave.vm.hostname = 'jenkins-slave'
    slave.vm.provision 'shell', inline: $install_tools
    slave.vm.synced_folder './html', '/home/vagrant/share', create: true
    slave.vm.network 'forwarded_port', guest: 22, host: 2020, adapter: 'docker0', host_ip: '172.17.0.1'
  end

  config.vm.define 'nginx' do |nginx|
    nginx.vm.provider "docker" do |d|
      d.image = "nginx"
    end
    nginx.vm.hostname = 'nginx'
    nginx.vm.synced_folder './html', '/usr/share/nginx/html', create: true
    nginx.vm.network 'forwarded_port', guest: 80, host: 8081
  end

end
