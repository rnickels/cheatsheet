# Create new virtual machine

1. get the machine image: hashicorp/precise64
2. start the virtual machine
3. ssh into the new virtual machine

~~~bash
$ mkdir vagrant-precise64 && cd vagrant-precise64
$ vagrant init hashicorp/precise64
$ vagrant up
$ vagrant ssh
~~~

# Destroy virtual machine

1. destroy the VM in the current directory

~~~bash
$ vagrant destroy 
~~~