# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.box_check_update = true
  config.ssh.insert_key = false
  #config.ssh.verify_host_key = :accept_new
  #config.ssh.verify_host_key = :accept_new_or_local_tunnel
  config.ssh.verify_host_key = :never #while developing

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = 1*1024
    vb.cpus = 1

    # Default NIC Type "virtio" needed for UEFI network boot.
    # Don't mix various NIX Types.
    # Server/Router VM has to be virtio, clients doesn't.
    # Connection issue if Server/Router is not virtio:
    #   Server(non-virtio) to Client(virtio): 1-2kb/s
    #   Server(virtio) to Client(virtio): 1gb/s
    #   Server(virtio) to Client(non-virtio): 1gb/s
    vb.default_nic_type = "virtio"

    # Detachable GUI notice:
    # quick and dirty patch in vagrant
    # https://github.com/hashicorp/vagrant/blob/1650f94215096109cd47153696efe34821a7a8ec/plugins/providers/virtualbox/action/boot.rb#L12
    # change "gui" to "separate" then activate "vb.gui = true" in Vagrantfile



    # Checking for Guest Additions.
    vb.check_guest_additions = false

    vb.customize ["modifyvm", :id, "--cpuexecutioncap", "75"]
    vb.linked_clone = false
  end

  # Hostmanager
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = false
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = false

  # Netbox Host.
  config.vm.define "netbox" do |netbox|
    netbox.vm.box = "centos/stream8"
    #netbox.vm.box = "debian/buster64"
    netbox.vm.graceful_halt_timeout = 600
    netbox.vm.hostname = "netbox.invalid"
    #netbox.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1", protocol: "tcp", auto_correct: false
    #netbox.vm.network "forwarded_port", guest: 443, host: 8443, host_ip: "127.0.0.1", protocol: "tcp", auto_correct: false
    netbox.vm.network "private_network", ip: "192.168.66.100"
    #netbox.vm.network "private_network", ip: "10.98.0.100", virtualbox__intnet: "pxe_net"

    netbox.vm.provision "ansible" do |ansible|
      ansible.playbook = "main.yml"
      ansible.limit = "netbox"
      ansible.config_file = "ansible.cfg"
      ansible.galaxy_role_file = "requirements.yml"
      # since ansible-4.0.0 which includes ansible-core 2.11.0 the compatibility check is broken
      # but works until ansible-3.4.0 which includes ansible-base 2.10.9
      #ansible.compatibility_mode = "2.0"
    end
  end

  config.vm.post_up_message = "Ready to go!"
end
