## Vagrant LAMP Stack

This project installs a LAMP server.  It is based on the https://github.com/r8/vagrant-lamp project (and cloned over a year ago). It's purpose is to provide an easy way to fire up as many virtual hosts as you like using data_bag site config files and good old vagrant provisioning.  It doesn't work yet.  The previous version (the one that was based on the [r8 project](https://github.com/r8/vagrant-lamp) did work, but since the author upgraded to a new Macbook Pro there is an issue with Chef.  Based on some googling, it seems that Chef made a breaking change late this past year that might have something to do with it.

**Documentation Note** The **[Installation](#installation)**, **[Setting Up a Site](#setting-up-a-site)** and **[Known Issues](#known-issues)** sections were authored specifically for this implementation of the base [r8 project](https://github.com/r8/vagrant-lamp).  The remainder of the documentation is essentially a cut-and-paste from the source project.

### Installation 

1. Using homebrew, install `vagrant` and `virtualbox`.

2. Clone this repo into your desired project directory.

  ```sh
cd ~/Development
clone git@github.com:motherlight/vagrant-lamp-stack.git
```

3. Launch the box

  ```sh
cd ~/Development/vagrant-lamp-stack
vagrant up
```
  **Note:** You will be prompted to provide the admin password for your computer (which is required to enable Vagrant to mount NFS).

4. After installation completes, ssh into the box:

  ```bash
vagrant ssh
```

### Setting Up a Site

The gitignore for this project ignores the following files/directories:

  ```rb
.vagrant
data_bags/sites/*.json
files/*
public/*
sql/*
```
The `.vagrant` directory is for the created vagrant stuff, so we don't want that version controlled. The `data_bags/sites` directory is where your site cong files (e.g. `lamp.jso`n) are stored. The `public` directory holds your project codebases. The other two are just desirable places to store database dumps and public files directories, just to keep things neat and tidy (and ignored by git). They are not included in the project so create them if you like.

1. Create a new `*.json` config file modelled on one of the examples in `/[project]/examples`. There is one for a plain-vanilla server (where docroot resolves to the domain itself i.e., `lamp.json`), the other specifying a custom docroot (`d7.json`). 

  Use the first part of the json file name (the asterisk in *.json) for the "id" and "host" attribute values.

2. Copy the json file to `[project]/data_bags/sites`

3. Checkout your codebase to `[project]/public`. Make sure the name you use for the codebase directory matches the "id" and "host" attributes of the associated json config file.
 
4. Add a domain mapping to your native `/etc/hosts` file

  For example, if you are working with lamp.json, you would add this line:

  ```bash
192.168.33.10  vagrant.lamp.dev
```
3. Reload with the provision flag

  ```bash
cd ~/Development/vagrant-lamp
vagrant reload --provision
```

### Known Issues

There is an issue with Chef.  See the following sample output of vagrant up:

  ```ruby

Craigs-MacBook-Pro:vagrant-lamp motherlight$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'precise32'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: vagrant-lamp_default_1425028738922_50445
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: hostonly
==> default: Forwarding ports...
    default: 3306 => 33066 (adapter 1)
    default: 80 => 8080 (adapter 1)
    default: 22 => 2222 (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if its present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default:
    default: Guest Additions Version: 4.2.0
    default: VirtualBox Version: 4.3
==> default: Configuring and enabling network interfaces...
==> default: Exporting NFS shared folders...
==> default: Preparing to edit /etc/exports. Administrator privileges will be required...
Password:
==> default: Mounting NFS shared folders...
==> default: Mounting shared folders...
    default: /tmp/vagrant-chef/0db5115ad4d86e21afc8cf4e71da556a/cookbooks => /Users/motherlight/Development/vagrant-lamp/cookbooks
    default: /tmp/vagrant-chef/a63afecf3d512814a80bd6eb561bb8d8/data_bags => /Users/motherlight/Development/vagrant-lamp/data_bags
==> default: Running provisioner: chef_solo...
    default: Installing Chef (latest)...
Generating chef JSON and uploading...
==> default: Running chef-solo...
==> default: stdin: is not a tty
==> default: [2015-02-27T09:19:36+00:00] INFO: Forking chef instance to converge...
==> default: [2015-02-27T09:19:36+00:00] INFO: *** Chef 12.0.3 ***
==> default: [2015-02-27T09:19:36+00:00] INFO: Chef-client pid: 1751
==> default: [2015-02-27T09:19:37+00:00] INFO: Setting the run_list to ["recipe[vagrant_main]", "recipe[vagrant_main::wordpress]", "recipe[vagrant_main::drupal]", "recipe[vagrant_main::nodejs]"] from CLI options
==> default: [2015-02-27T09:19:37+00:00] INFO: Run List is [recipe[vagrant_main], recipe[vagrant_main::wordpress], recipe[vagrant_main::drupal], recipe[vagrant_main::nodejs]]
==> default: [2015-02-27T09:19:37+00:00] INFO: Run List expands to [vagrant_main, vagrant_main::wordpress, vagrant_main::drupal, vagrant_main::nodejs]
==> default: [2015-02-27T09:19:37+00:00] INFO: Starting Chef Run for precise32
==> default: [2015-02-27T09:19:37+00:00] INFO: Running start handlers
==> default: [2015-02-27T09:19:37+00:00] INFO: Start handlers complete.
==> default: [2015-02-27T09:19:37+00:00] ERROR: Running exception handlers
==> default: [2015-02-27T09:19:37+00:00] ERROR: Exception handlers complete
==> default: [2015-02-27T09:19:37+00:00] FATAL: Stacktrace dumped to /var/chef/cache/chef-stacktrace.out
==> default: [2015-02-27T09:19:37+00:00] ERROR: Cookbook loaded at path(s) [/tmp/vagrant-chef/0db5115ad4d86e21afc8cf4e71da556a/cookbooks/drush] has invalid metadata: The `name' attribute is required in cookbook metadata
==> default: [2015-02-27T09:19:37+00:00] FATAL: Chef::Exceptions::ChildConvergeError: Chef run process exited unsuccessfully (exit code 1)
Chef never successfully completed! Any errors should be visible in the
output above. Please fix your recipes so that they properly complete.
```
#### Possible Causes

Some investigation seems to suggest that there was a breaking change in Chef. This particular SO issue is similar to the ouput issue for this project:

* http://stackoverflow.com/questions/27810967/error-cookbook-loaded-at-paths-has-invalid-metadata

### Apache virtual hosts

You can add virtual hosts to apache by adding a file to the `data_bags/sites`
directory. The docroot of the new virtual host will be a directory within the
`public/` folder matching the `host` you specified. Alternately you may specify
a docroot explicitly by adding a `docroot` key in the json file.

### MySQL

The guests local 3306 port is available on the host at port 33066. It is also available on every domain. Logging in can be done with username=root, password=vagrant.

### phpMyAdmin

phpMyAdmin is available on every domain. For example:

    http://local.dev/phpmyadmin

### XDebug and webgrind

XDebug is configured to connect back to your host machine on port 9000 when 
starting a debug session from a browser running on your host. A debug session is 
started by appending GET variable XDEBUG_SESSION_START to the URL (if you use an 
integrated debugger like Eclipse PDT, it will do this for you).

XDebug is also configured to generate cachegrind profile output on demand by 
adding GET variable XDEBUG_PROFILE to your URL. For example:

    http://local.dev/index.php?XDEBUG_PROFILE

Webgrind is available on each domain. For example:

    http://local.dev/webgrind

It looks for cachegrind files in the `/tmp` directory, where xdebug leaves them.

**Note:** xdebug uses the default value for xdebug.profiler_output_name, which 
means the output filename only includes the process ID as a unique part. This 
was done to prevent a real need to clean out cachgrind files. If you wish to 
configure xdebug to always generate profiler output 
(`xdebug.profiler_enable = 1`), you *will* need to change this setting to 
something like
 
    xdebug.profiler_output_name = cachegrind.out.%t.%p
    
so your call to webgrind will not overwrite the file for the process that 
happens to serve webgrind. 

### Mailcatcher

All emails sent by PHP are intercepted by MailCatcher. So normally no email would be delivered outside of the virtual machine. Instead you can check messages using web frontend for MailCatcher, which is running on port 1080 and also available on every domain:

    http://local.dev:1080

### Composer

Composer binary is installed globally (to `/usr/local/bin`), so you can simply call `composer` from any directory.


