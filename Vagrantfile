# coding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.require_version '= 2.2.16'

# Temporary monkey patch for Vagrant 2.2.7/vagrant-aws incompatibility
# https://github.com/mitchellh/vagrant-aws/issues/566
class Hash
  def slice(*keep_keys)
    h = {}
    keep_keys.each { |key| h[key] = fetch(key) if has_key?(key) }
    h
  end unless Hash.method_defined?(:slice)
  def except(*less_keys)
    slice(*keys - less_keys)
  end unless Hash.method_defined?(:except)
end

Vagrant.configure('2') do |config|
  config.vagrant.plugins = {
    'vagrant-aws' => {'version' => '~> 0.7.2'},
  }

  config.vm.box = 'dummy'
  config.vm.box_url = 'https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box'
  config.vm.allowed_synced_folder_types = [:rsync]
  config.ssh.username = 'ubuntu'
  config.ssh.private_key_path = ENV['vagrant_2_2_16_test_key']

  config.vm.provider 'aws' do |aws|
    aws.ami = 'ami-01de8ddb33de7a3d3' # An ubuntu 20.04 image
    aws.instance_type = 't3.nano'
    aws.keypair_name = ENV['vagrant_2_2_16_test_key']

    aws.associate_public_ip = true
    aws.subnet_id = ENV['vagrant_2_2_16_subnet_id']
    aws.security_groups = [
      ENV['vagrant_2_2_16_security_group_id']
    ]
    aws.tags = {
      'Name' => "Vagrant 2.2.16 testing #{ENV['vagrant_2_2_16_test_key']}",
    }
  end

  config.vm.provision 'shell', inline: <<-SHELL
    true
  SHELL
end
