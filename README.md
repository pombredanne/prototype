# Macrocontainers for Legacy Applications

LeApp is a "Minimum Viable Migration" utility that aims to
decouple virtualized applications from the operating system
kernel included in their VM image by migrating them into
macrocontainers that include all of the traditional components
of a stateful VM (operating system user space, application
runtime, management tools, configuration files, etc), but
use the kernel of the container host rather than providing
their own.

## Migration prototype

The migration prototype currently handles exactly two cases:

* migrating from a CentOS 6 VM to a macrocontainer running on
  a CentOS 7 container host.
* migrating from a CentOS 7 VM to a macrocontainer running on
  a CentOS 7 container host.

### Installing LeApp

#### Installation on RHEL / CentOS 7
* Install the EPEL7 repositories

  On RHEL:

      $ yum install -y http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-9.noarch.rpm

  On CentOS:

      $ yum install epel-release

* Install the repository

      $ cd /etc/yum.repos.d && sudo wget https://copr.fedorainfracloud.org/coprs/evilissimo/leapp/repo/epel-7/evilissimo-leapp-epel-7.repo

* Install LeApp

      $ sudo yum install leapp-cockpit


#### Installation on Fedora
* Install the copr plugin

      $ sudo dnf install dnf-plugins-core

* Install the repo

      $ sudo dnf copr enable evilissimo/leapp

* Install LeApp

      $ sudo dnf install leapp-cockpit


## Development, Testing and Demo setup

### Setting up to run the prototype demonstration

If the integration tests haven't been run, first install the testing
instance of the CLI:

    $ pipsi --bin-dir $PWD/bin install --python `which python2.7` $PWD/src

The integration tests do this automatically, so this step can be skipped if
those have already been run.

GCC is needed to compile vagrant plugins

    $ sudo yum install qemu libguestfs-tools-c libvirt libvirt-devel ruby-devel gcc qemu-kvm nmap libffi-devel


The prototype requires Vagrant with some relevant plugins:

    $ sudo vagrant plugin install ansible hitimes nio4r vagrant-libvirt

And passwordless access to VM management operations:

    $ sudo usermod -aG vagrant,libvirt $USER

You will need to log out and back in again, or start a new user
session with `su $USER`, to get the new group memberships to take
effect.

Finally, start all the demonstration VMs by running:

    $ demo/vmctl.sh

This script iterates over all the subdirectories of `demo/enabled/vmdefs` and runs
`vagrant up --provision`.

### Running the demonstration via the CLI

First, check that the source VM is showing the
PHP admin login page at port 9000, while the target VM isn't
running a HTTP server.

The demo admin login credentials are:

* Username: `root`
* Password: `toor`

Then, from the base of the local clone, run:

    $ sudo bin/leapp-tool migrate-machine \
           --identity integration-tests/config/leappto_testing_key \
           --user vagrant --tcp-port 9000:9000 -t centos7-target centos6-app-vm

The target VM should now be showing the PHP admin page,
with the same information as the source VM.


### Running the demonstration via Cockpit

Link the Cockpit plugin (if not already linked):

    $ mkdir -p ~/.local/share/cockpit
    $ ln -snf $PWD/cockpit ~/.local/share/cockpit/leapp

Link the `leapp` project directory (if not already linked):

    $ sudo ln -snf $PWD /opt/leapp

Open Cockpit in your browser:

    http://localhost:9090

When authenticating, check the option to allow
Cockpit to retain your password for later
privilege escalation.

Open "Tools->Le-App" from the
navigation menu.

Check that the application link for the source VM
show the PHP info page, while the target VM isn't
running a HTTP server.

Click the "Migrate" button (this is currently
hardcoded to migrate `centos6-app-vm` to `centos7-target`)

The target VM should now be showing the PHP info page,
with the same information as the source VM.


Known Constraints
-----------------

Currently known constraints on this approach:

* SELinux process separation is not available inside
  the resulting macrocontainer


Key limitations in the current implementation:

* Remote access to systems requires Vagrant
  managed VMs running locally under libvirt
