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

- `config.vagrant.host` (`string`, `symbol`) [`:detect`] - Tells Vagrant what kind of host is running, which is only necessary for setting up NFS folders if those are enabled. This should be fine to be left as default.

- `config.vagrant.plugins` (`string`, `array`, `hash`) - Which plugins Vagrant will install for the local project. They can be defined as follows:
```Vagrant
config.vagrant.plugins = "vagrant-plugin"
config.vagrant.plugins = ["vagrant-plugin", "vagrant-other-plugin"]
config.vagrant.plugins = {"vagrant-scp" => {"version" => "1.0.0"}}
```
- `config.vagrant.sensitive` (`string`, `array`) - Value or list of values not to be listed by Vagrant in normal output or loggers. Useful for sensitive data such as passwords. Example: `config.vagrant.sensitive = ["MySQLPasswd", ENV["MYSQL_PW"]]`

## Boxes

Boxes are not unlike `Dockerfile`s downloaded from DockerHub. They follow a `username/box` naming scheme, and can be fetched from the Vagrant CLI via `vagrant box add {username}/{boxname}`. Boxes can also be added from a file path or a URL.

You can specify which box to use inside the Vagrantfile by using `config.vm.box = "{username}/{boxname}`. Other variables that can be set are `box_version`, `box_check_update`,  and `box_url`.

### Creating a Box
A base box is a box that contains only the bare minimum; they are usually made from install media rather than repackaging an existing environment. If you can avoid creating one, you generally should.

Base boxes will usually have:
- Package Manager
- SSH
- SSH user for Vagrant
- Maybe some kind of provisioning software

Boxes are platform specific, so if you create one for VirtualBox it will __only__ work with VirtualBox. 
Provider Specific boxes:
- [Docker](https://www.vagrantup.com/docs/docker/boxes.html)
- [Hyper-V](https://www.vagrantup.com/docs/hyperv/boxes.html)
- [VMware](https://www.vagrantup.com/docs/vmware/boxes.html)
- [VirtualBox](https://www.vagrantup.com/docs/virtualbox/boxes.html)

_If you can avoid adding a peripheral, do so. Ensure your box uses as little resources as possible._

#### Default User Settings
There are certain defaults that vagrant expects. You shouldn't use these if you have no plans to distribute your box.

By default, you should expect/are expected to have:
- A `vagrant` user, that uses [the default keys](https://github.com/hashicorp/vagrant/tree/master/keys) in `~/.ssh/authorized_keys` with `0700` folder permissions and `0600` file permissions.
- A root password of `vagrant`.
- Passwordless sudo, which can be accomplished with `vagrant ALL=(ALL) NOPASSWD: ALL`
- In SSH settings, `UseDNS` is `no`

#### [Special Considerations for Windows Boxes](https://www.vagrantup.com/docs/boxes/base.html#windows-boxes)
Because who doesn't love gimping the security of an already insecure system?

You'll need to disable UAC, disable complex passwords, disable "shutdown tracker", ans disable the "server manager" from starting at login. In addition to disabling UAC in settings, you need to disable it again in the registry(???).

Windows Server 2003 and Windows XP are not supported.

#### Testing the Box
```vagrant
vagrant box add --name box-name --provider=provider-name /path/to/new.box
vagrant init box-name
vagrant up
```

## Notes on SSH
You can easily SSH into your Vagrant box with `vagrant up && vagrant ssh`. The first brings the machine up, the second connects to it. 

`vagrant destroy` will not remove the downloaded box file -- `vagrant box remove` is needed to eliminate all trace.

It is recommended that `UseDNS` is set to `no` to prevent waitin for a possible timeout when trying to connect. 

## Sync'd Folders
You probably shouldn't do anything destructive because the `/vagrant` directory on the guest has a link to the host.

If you _do_ want to remap this, you can do so via `config.vm.synced_folder`. See more details at the [Synced Folders Basic Usage Documentation](https://www.vagrantup.com/docs/synced-folders/basic_usage.html)

## [Provisioning](https://www.vagrantup.com/docs/provisioning/basic_usage.html)
You can tell Vagrant what to do after the OS is loaded via the `config.vm.provision` setting. 

Ex: `config.vm.provision :shell, path: "bootstrap.sh"`

They can also have `run: "always"` with `always` being subbed for `once` or `never`. Default is `once`.

If a script is small, it can be embedded by using `inline` in stead of `path`.

You are not limited to one provisioner. Provisioners are run in the order that they are defined __from the outside in.__

```vagrant
Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo foo"

  config.vm.define "web" do |web|
    web.vm.provision "shell", inline: "echo bar"
  end

  config.vm.provision "shell", inline: "echo baz"
end
```
This will output "foo baz bar". If you want to only run a specific provisioner, you can pass in the `--provision-with {name}` flag.

## Networing
### [Port Forwaring](https://www.vagrantup.com/docs/networking/forwarded_ports.html)
Ports can be forwarded with `config.vm.network :forwarded_port, guest: {port}, host: {port}`.
### Additional Networks
This varies from providers and cannot be generally outlined.

_Even Vagrant is a little iffy on their network configs. Just refer to [the documentation](https://www.vagrantup.com/docs/networking/public_network.html) for this stuff.

## Vagrant Share
At any point you can direct anyone else with an internet connection to your Vagrant environment via `vagrant share`. __This is not for use in production.__

`vagrant-share` is a plugin that must be installed, as it is not shipped by default, and relies on `ngrok` to work.

```vagrant
vagrant plugin install vagrant-share 
```

## Teardown
Vagrant can `suspend`, `halt`, or `destroy`.

__Suspend__ will save the current state of the guest and stop it. It's the equivalent of a state freeze.

__Halt__ will gracefully shut down the guest. 

__Destroy__ will remove all traces of the guest from your machine.
