Puppet
------

Installs `puppet version 5 <http://puppetlabs.com/>` From the site 
repository `<http://apt.puppetlabs.com/>` and optionally applies a
manifest inside the chroot. You can also have it copy your puppet
configuration into the image so it is readily available once the image
is booted.

- installs puppet agent software
- installs puppet modules with a puppetfile (useful in masterless environments)
  on precondition that assets are well placed in their proper directory

Rationale and use case in a masterless setup
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You want to use this plugin when you wish to create an image and to be able to
manage that image with Puppet. You have a Puppet 5 setup in mind and thus you 
want the image to contain the puppet agent software from the puppetlabs repo. 
You want it to almost contain everything you need to get it up and running 
This plugin does just that!
While you're at it, throw in some modules from the forge as well! This is now
 made possible with 'r10k <https://github.com/puppetlabs/r10k>', so you can 
 properly isntall modules in the target environment.

This is primarily useful when you have a very limited collection of nodes you 
wish to manage with puppet without to having to set up an entire puppet infra-
structure. This allows you thus to work "masterless". 

My use case personally is to be able to create a few images for 'genesis' of an
 infrastructure. E.g.: Set up a fast deployable working virtual machine as a 
 puppet master, mail server and gitlab server. From there you can build 
 anything you want.

About Master/agent setups
~~~~~~~~~~~~~~~~~~~~~~~~~

If you wish to use this plugin in an infrastructure where a puppet master is 
present, you should evaluate what your setup is. In a puppet OSS server setup 
it can be useful to just use the plugin without any manifests, assets or 
modules included. 
In a puppet PE environment you will probably not need this plugin since the PE 
server console gives you an URL that installs the agent corresponding to your 
PE server. 

Settings
~~~~~~~~

-  ``manifest``: Path to the puppet manifest that should be applied.
   ``optional``
-  ``assets``: Path to puppet assets. The contents will be copied into
   ``/etc/puppetlabs`` on the image. Any existing files will be overwritten.
   ``optional``
-  ``install_modules``: A list of modules you wish to install available from 
   `<https://forge.puppetlabs.com/>` inside the chroot. It will assume a FORCED
   install of the modules.
   This list is a list of tuples. Every tuple must at least contain the module 
   name. A version is optional, when no version is given, it will take the 
   latest version available from the forge. 
   Format: [module_name (required), version (optional)]
-  ``enable_agent``: Whether the puppet agent daemon should be enabled. 
   ``optional - not recommended``. disabled by default. UNTESTED
   
An example bootstrap-vz manifest is included in the ``KVM`` folder of the 
manifests examples directory.
      
Limitations
~~~~~~~~~~~
(Help is always welcome, feel free to chip in!)
General:

- This plugin only installs the puppet5 package for now, needs to be extended to 
  be able to install the version of choice.
- This pluginis only compatible with Debian versions Wheezy, Jessie and 
  Stretch. These are the only Debian distributions supported by puppetlabs.

Manifests:

- Running puppet manifests is not recommended and untested, see below

Assets:

- The assets path must be ABSOLUTE to your manifest file. Meaning: full path. 

install_modules:

- Installation of modules is performed by the use of r10k

UNTESTED:

- Enabling the agent and applying the manifest inside the chrooted environment.
	Keep in mind that when applying a manifest when enabling the agent option,
	the system is in a chrooted environment. This can prevent daemons from 
	running	properly (e.g. listening to ports), they will also need to be shut 
	down gracefully (which bootstrap-vz cannot do) before unmounting the 
	volume. It is advisable to avoid starting any daemons inside the chroot at 
	all.
