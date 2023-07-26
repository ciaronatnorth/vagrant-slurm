# -*- mode: ruby -*-
# vi: set ft=ruby :

CONTROLLER_SOCKETS=1
CONTROLLER_CORES_PER_SOCKET=4
CONTROLLER_CORES=4 # product of sockets and cores_per_socket

COMPUTE_SOCKETS=2
COMPUTE_CORES_PER_SOCKET=4
COMPUTE_CORES=8 # product of sockets and cores_per_socket
NUMA_DOMAIN_1="0-3"
NUMA_DOMAIN_2="4-7"

$script = <<-SCRIPT
    sudo echo "192.168.56.2 controller" | sudo tee -a /etc/hosts
    #sudo echo "192.168.56.3 dbserver" | sudo tee -a /etc/hosts
    sudo echo "192.168.56.4 compute1" | sudo tee -a /etc/hosts
    sudo echo "192.168.56.5 compute2" | sudo tee -a /etc/hosts
SCRIPT

Vagrant.configure("2") do |config|

  #config.vm.box = "rockylinux/8"
  config.vm.box = "generic/rocky8"

  config.vm.synced_folder "../rundata", "/rundata"

  config.vm.define "controller" do |machine|
    machine.vm.hostname = "controller"
    machine.vm.network "private_network", ip: "192.168.56.2", hostname: true
    machine.vm.provider :libvirt do |libvirt|
      libvirt.cpus = CONTROLLER_CORES
      libvirt.memory = 512
      libvirt.cputopology :sockets => CONTROLLER_SOCKETS, :cores => CONTROLLER_CORES_PER_SOCKET, :threads => '1'
      #libvirt.numa_nodes = [
      #  {:cpus => "0-1", :memory => "256"},
      #  {:cpus => "2-3", :memory => "256"}
      #]
    end
  end

  ncomputes=2

  # set up some "compute#" nodes.
  (1..ncomputes).each do |i|
    config.vm.define "compute#{i}" do |machine|
      machine.vm.hostname = "compute#{i}"
      machine.vm.network "private_network", ip: "192.168.56.#{i+3}", hostname: true
      machine.vm.provider :libvirt do |libvirt|
        libvirt.cpus = COMPUTE_CORES
        libvirt.memory = 512
        libvirt.cputopology :sockets => COMPUTE_SOCKETS, :cores => COMPUTE_CORES_PER_SOCKET, :threads => '1'
        libvirt.numa_nodes = [
          {:cpus => NUMA_DOMAIN_1, :memory => "256"},
          {:cpus => NUMA_DOMAIN_2, :memory => "256"}
        ]
      end
    end
  end

  # set up /etc/hosts on the VMs
  config.vm.provision "shell", inline: $script

  config.vm.provision "ansible" do |ansible|
    #ansible.verbose = "vvv"
    ansible.playbook = "provisioning/playbook.yml"
    ansible.groups = {
      "computes" => ["compute[1:#{ncomputes}]"]
    }
    ansible.extra_vars = {
      compute: {
        sockets: COMPUTE_SOCKETS,
        cores_per_socket: COMPUTE_CORES_PER_SOCKET,
	total_cores: COMPUTE_CORES
      }
    }
  end

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

end
