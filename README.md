OpenShift Quickstart on Vagrant
===============================

Need [OpenShift](https://www.openshift.com/)?

This solution will get you up and running in no time. The only requirement is
[Vagrant](https://www.vagrantup.com/) (running on top of VirtualBox). You don't even need to be on
Linux, because Vagrant will bring up a virtual machine with Linux.

Why do we need this? Generally, bootstrapping OpenShift in a production environment can be complex.
For example, check out [these blueprints](https://github.com/redhat-nfvpe/base-infra-bootstrap) for
bootstrapping on CentOS Atomic Host using Ansible. But for development and testing purposes you
might not need the whole stack. This solution keeps things as straightforward as possible.


How To
------

Just clone this repository, change to this directory, and run `vagrant up`. That's it!

Within a few minutes you should see a message with the URL and user/password to access the web
console. (Note that the web console uses a custom certificate, so you will need to configure your
web browser to accept it.) 

Other than the web console, you can access your OpenShift instance using any client (such as `oc`)
running on your real host machine against that URL. But you can also get direct shell access:

1) Run `vagrant ssh` to get shell access to the virtual machine. (Your user is `vagrant` and your
   password is `vagrant` and you have sudo rights.) We've pre-installed [Byobu](http://byobu.co/)
   to make life a bit easier.
2) Run `host/openshift-shell` to get shell access to the container. (You are root.)
3) You can now run `oc`, `openshift`, etc. If you need access to files from your real host machine,
   just put them (or link them) in the `host/container` directory, which is shared there.


How It Works
------------

What happens behind the scenes when you run `vagrant up` is this:

1. Vagrant will download a Fedora box (from [here](https://app.vagrantup.com/boxes/search)) and
   create a virtual machine based on it. Our `host` directory here will be shared with it. Note that
   the machine will have a "public" IP address, on the same network as your host. (If you have
   multiple network interfaces on your machine then Vagrant will ask you which one to use.)
2. Within the virtual machine we will run `host/host-setup`, which will install Docker (from the
   default Fedora repositories).
3. Within the virtual machine we will then then run `host/bootstrap-openshift`, which will download
   the OpenShift Origin all-in-one Docker image, explicitly *without* Ansible (from
   [here](https://hub.docker.com/r/openshift/origin/tags/)) . Our `host/container` directory will be
   shared with the container (note the nesting!). 
4. Within the container we will run `container/install-web-console` and `container/install-templates` to add
   some basic functionality above the vanilla OpenShift. (To do this we will clone some git
   repositories, which contain the templates.)

The only tricky part in this scheme is making sure the port inside the container gets forwarded
to its containing virtual machine, and then to your real host machine.
