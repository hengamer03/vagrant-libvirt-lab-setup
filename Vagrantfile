Vagrant.configure("2") do |config|
  # Define the box to be used for all VMs
  BOX_NAME = "generic/rhel9"

  config.vm.provider :libvirt do |libvirt|
    # Uncomment and configure the following lines if needed
    libvirt.driver = "kvm"
    #libvirt.host = 'localhost'
    libvirt.uri = 'qemu:///system'
    libvirt.cpu_mode = "host-passthrough"
    libvirt.storage_pool_name = "default"
  end

  config.vm.define "test1" do |vm1|
    vm1.vm.box = BOX_NAME
    vm1.vm.hostname = "vm1.example.com"
    vm1.vm.provider :libvirt do |domain|
      domain.memory = 2048
      domain.cpus = 1
      domain.storage :file, size: '2G', bus: 'virtio'
    end
    vm1.vm.network :private_network,
                   ip: '192.168.122.10',
                   libvirt__netmask: '255.255.255.0',
                   libvirt__network_name: 'default',
                   libvirt__forward_mode: 'none'
  end

  config.vm.define "test2" do |vm2|
    vm2.vm.box = BOX_NAME
    vm2.vm.hostname = "vm2.example.com"
    vm2.vm.provider :libvirt do |domain|
      domain.memory = 2048
      domain.cpus = 1
    end
    vm2.vm.network :private_network,
                   ip: '192.168.122.20',
                   libvirt__netmask: '255.255.255.0',
                   libvirt__network_name: 'default',
                   libvirt__forward_mode: 'none'
  end

  # Uncomment the following block to enable the third VM
  # config.vm.define "test3" do |vm3|
  #   vm3.vm.box = BOX_NAME
  #   vm3.vm.hostname = "vm3.example.com"
  #   vm3.vm.provider :libvirt do |domain|
  #     domain.memory = 2048
  #     domain.cpus = 1
  #   end
  #   vm3.vm.network :private_network,
  #                  ip: '192.168.122.30',
  #                  libvirt__netmask: '255.255.255.0',
  #                  libvirt__network_name: 'default',
  #                  libvirt__forward_mode: 'none'
  # end
end
