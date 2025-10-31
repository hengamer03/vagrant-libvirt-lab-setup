# Vagrant + Libvirt Virtualization Setup Guide (Fedora / RHEL)

** This is transelated using chatgpt from Norwegian to english **

This document is a simple guide for setting up a basic virtualization environment with a virtual network on Linux, and then using Vagrant to easily spin up VM's.
The repository's files are the same ones shown in the examples here


**Note:** This guide is for Fedora / RHEL. Libvirt setup may differ on other distros.

---

## Step 1. Install virtualization packages

```bash
sudo dnf install qemu-kvm libvirt virt-install virt-viewer libvirt-devel
```

---

## Step 2. Edit libvirt config

Open:

```bash
sudo vim /etc/libvirt/qemu.conf
```

Comment out these lines:

```
user = "qemu"
group = "libvirt"
```

---

## Step 3. Add your user to qemu group

```bash
sudo usermod -aG qemu $USER
```

---

## Step 4. Enable and start libvirtd

```bash
sudo systemctl enable --now libvirtd
```

---

## Step 5. Create private network XML

Create `vagrant-private.xml`:

```xml
<network>
  <name>vagrant-private</name>
  <bridge name="virbr1"/>
  <ip address="192.168.56.1" netmask="255.255.255.0">
    <dhcp>
      <range start="192.168.56.100" end="192.168.56.200"/>
    </dhcp>
  </ip>
</network>
```

This defines a NAT virtual network on interface `virbr1`.

---

## Step 6. Load and enable the network in libvirt

```bash
sudo virsh net-define vagrant-private.xml
sudo virsh net-start vagrant-private
sudo virsh net-autostart vagrant-private
sudo virsh net-list
```

Expected output:

```
 Name              State    Autostart   Persistent
----------------------------------------------------
 default           active   yes         yes
 vagrant-private   active   yes         yes
```

---

## Step 7. Install Vagrant

Install Vagrant from:
https://developer.hashicorp.com/vagrant/install

---

## Step 8. Install libvirt plugin for Vagrant

```bash
vagrant plugin install vagrant-libvirt
```

---

## Example Vagrantfile for two RHEL9 VM's

```ruby
Vagrant.configure("2") do |config|
  # Define the box to be used for all VMs
  BOX_NAME = "generic/rhel9"

  config.vm.provider :libvirt do |libvirt|
    libvirt.driver = "kvm"
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

  # Uncomment to enable third VM
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
```

---

## start the VM's

To start the VM's, simply type into the terminal: 
```bash
vagrant up
```

when thats done, you can ssh into the VM's

```bash
vagrant ssh testvm1 #or whatever name you gave to the VM
``` 

You are ready to build and test VM's with Vagrant and Libvirt.

