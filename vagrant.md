# Vagrant

[eBook by HashiCorp](https://www.vagrantup.com/intro/getting-started/)

## Getting Started

> Vagrant provides an easy to configure, reproducible, and portable work environments built on IS technology. Machines are provisioned via VirtualBox, VMware, AWS, etc.
>
> Then shell scripts / chef / puppet / etc are used to configure the software on the VM.
[Read More](https://www.vagrantup.com/intro/index.html)

### Quick Start
Running the two commands below will create and start a VirtualBox VM running Ubuntu 12.04 LTS.
```vagrantfile
$ vagrant init hashicorp/precise64
$ vagrant up
```
You can then SSH into it with `vagrant ssh`, or take it down/delete it via `vagrant destroy`.

### Project Setup
Vagrant projects are operated based around a [Vagrantfile](https://www.vagrantup.com/docs/vagrantfile/). 

You can create a Vagrant project in a directory by running `vagrant init`.

## The Vagrantfile
The Vagrantfile is meant to be committed to a VCS. That way, anyone will be able to get the exact same setup as you.

The primary function is to tell the VM how to be configured. They have a Ruby syntax, but is mostly just variable assignment. The Vagrantfile is found by climbing up the tree until one is found, or setting it manually with `VAGRANT_CWD`. e.g. 
```
? ${VAGRANT_CWD}/Vagrantfile
? ${VAGRANT_CWD}/../Vagrantfile
! ${VAGRANT_CWD}/../../Vagrantfile
```

More than one vagrantfile can be loaded. The order of priority is not unlike CSS.

Vagrant files will begin in the following format:
```vagrantfile
Vagrant.configure("2") do |config|
    #...
end
```
Different versions of vagrantfiles will not be forward compatible. `"1"` will be 1.0.x. `"2"` will be compatible from versions 1.1 to 2.0.x. 
Multiple configurations can be mixed:
```vagrantfile
Vagrant.configure("1") do |config|
    #...
end

Vagrant.configure("2") do |config|
    #...
end
```
You can specify which versions are required by using `Vagrant.require_version ">= 1.3.5", "< 1.4.0"`. The Vagrantfile will only load if it matches the criteria.

Because Vagrantfiles are Ruby, you can use Ruby to extend them. For example -- if you want to define three VMs that are the same:
```Vagrantfile
(1..3).each do |i|
    config.vm.define "node-#{i}" do |node|
        node.vm.provision "shell",
            inline: "echo hello from node-#{i}"
    end
end
```
__[[!]](https://www.vagrantup.com/docs/vagrantfile/tips.html) Multi-machine definitions and provider overrides are lazy-loaded and can cause issues in configs!__ If you have a variable `i` that is modified in a loop (like a `for` loop), `i` will be the same between the loops.

__[[!]](https://www.vagrantup.com/docs/vagrantfile/tips.html) Host locale environment variables are passed to the guest!__ This can cause issues on the "guest" sessions. You can override locale in the Vagrantfile:
```vagrantfile
ENV["LC_ALL"] = "en_US.UTF-8"

Vagrant.configure("2") do |config|
    (1..3).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.provision "shell", inline: "echo node-#{i} LANG=$LANG"
end
```

It's at this point that the book/docs just vomit `config` settings with what I can only assume is every single setting. Instead of writing a proper summary, instead enjoy these topical recaps with links to the pages.

__[config.vm](https://www.vagrantup.com/docs/vagrantfile/machine_settings.html)__ modifies the _configuration_ of the Vagrant managed machine.

__[config.ssh](https://www.vagrantup.com/docs/vagrantfile/ssh_settings.html)__ modifies settings related to configuring _how Vagrant will access the machine over SSH_.

__[config.winrm](https://www.vagrantup.com/docs/vagrantfile/winrm_settings.html)__ only applies if the guest is a Windows VM. Other Windows specific settings include __[config.winssh](https://www.vagrantup.com/docs/vagrantfile/winssh_settings.html)__.

__[config.vagrant](https://www.vagrantup.com/docs/vagrantfile/vagrant_settings.html)__ modifies the behaviour of Vagrant itself.
