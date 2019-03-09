VAGRANTFILE_API_VERSION = "2"

require 'yaml'
settings = YAML.load_file 'vagrant.yml'

Vagrant.configure( VAGRANTFILE_API_VERSION ) do |config|

  config.vm.box = settings['vagrant_box']
  config.vm.hostname = settings['vm_hostname']
  config.vm.network :private_network, ip: settings['ip_address']

  config.ssh.insert_key = false

  config.vm.synced_folder "../S2T/", "/srv/S2T/", id: "S2T"
  
  config.vm.provider :virtualbox do |v|
    v.name = settings['vm_name']
    v.memory = settings['vm_memory']
    v.cpus = settings['vm_cpu_count']
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--ioapic", "on"]
    v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/vagrant", "1"]
  end

  # Enable provisioning with Ansible.
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "ansible/site.yml"
    ansible.galaxy_role_file = "ansible/requirements.yml"
    ansible.config_file = "ansible/ansible.cfg"
    ansible.become = true
    ansible.extra_vars = {
        vagrant_ip_address: settings['ip_address'],
        ansible_ssh_user: 'vagrant',
        vagrant: true,
        zsh_user: 'vagrant'
    }
  end
end
