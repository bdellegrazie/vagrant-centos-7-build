# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.require_version ">= 2.1.4"

begin
  require_relative('settings')
rescue LoadError
end

ANSIBLE_GROUPS ||={}
ANSIBLE_TAGS ||= nil
ANSIBLE_VERBOSE ||= nil
ANSIBLE_EXTRA_VARS ||= {}
ansible_groups = {}
ansible_groups.merge!(ANSIBLE_GROUPS)
ansible_galaxy_role_file ||='ansible/requirements.yml'

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"

  config.vagrant.plugins = ["vagrant-hosts", "vagrant-vbguest"]

  # View the documentation for the provider you are using for more
  # information on available options.
  config.vm.provider :virtualbox do |vb|
    vb.gui = false
    vb.memory = 4096
    vb.cpus = 2
    vb.customize ["modifyvm", :id, "--ostype", "Redhat_64"]
    vb.customize ["modifyvm", :id, "--nictype1", "virtio"]
    vb.customize ["storageattach", :id, "--storagectl", "IDE", "--device", "0", "--port", "0", "--nonrotational", "on", "--discard", "on"]
    # timesync equivalent to 2.5 minutes in ms)
    vb.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", "150000"]
  end

  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
  config.vbguest.no_remote = true
  config.ssh.forward_agent = true
  config.ssh.insert_key = false
  #config.vm.network :forwarded_port, guest: 19999, host: 19999
  #config.vm.network :forwarded_port, guest: 80, host: 8080

  config.vm.provision "ansible", type: "ansible", run: "once" do |ansible|
    ansible.galaxy_command = "ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path}"
    ansible.galaxy_roles_path = 'ansible/galaxy_roles'
    ansible.galaxy_role_file = ansible_galaxy_role_file
    ansible.playbook = "ansible/playbook.yml"
    ansible.groups = ansible_groups
    ansible.tags = ANSIBLE_TAGS
    ansible.extra_vars = ANSIBLE_EXTRA_VARS
    ansible.verbose = ANSIBLE_VERBOSE
  end
end
