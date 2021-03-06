Knife OpenStack
===============

This is the official Opscode Knife plugin for OpenStack Compute (Nova). This plugin gives knife the ability to create, bootstrap and manage instances in OpenStack Compute clouds. It has been tested against the `diablo-stable` branch in configurations using Keystone against the OpenStack API (as opposed to the EC2 API).

Please refer to the CHANGELOG.md for version history.

# Installation #

Be sure you are running the latest version Chef. Versions earlier than 0.10.0 don't support plugins:

    $ gem install chef

This plugin currently depends on a patches waiting to be incorporated into Fog. You will need to use Fog 1.1.2 from here: https://github.com/mattray/fog To install it, run:

    $ cd /tmp
    $ git clone https://github.com/fog/fog.git
    $ cd fog
    $ gem build fog.gemspec
    $ gem install fog-1.3.1.gem

This plugin is distributed as a Ruby Gem, but is not available on Rubygems.org because of the missing Fog dependencies. To install it, run:

    $ cd /tmp
    $ git clone -b 0.6.0 git://github.com/mattray/knife-openstack.git
    $ cd knife-openstack
    $ gem build knife-openstack.gemspec
    $ gem install knife-openstack-0.6.0.gem

Depending on your system's configuration, you may need to run this command with root privileges.

# Configuration #

In order to communicate with an OpenStack Compute cloud's OpenStack API you will need to tell Knife your OpenStack Compute API endpoint, your Dashboard username and password. The easiest way to accomplish this is to create these entries in your `knife.rb` file:

    knife[:openstack_username] = "Your OpenStack Dashboard username"
    knife[:openstack_password] = "Your OpenStack Dashboard password"
    ### Note: If you are not proxying HTTPS to the OpenStack auth port, the scheme should be HTTP
    knife[:openstack_auth_url] = "http://cloud.mycompany.com:5000/v2.0/tokens"

If your knife.rb file will be checked into a SCM system (ie readable by others) you may want to read the values from environment variables:

    knife[:openstack_username] = "#{ENV['OS_USERNAME']}"
    knife[:openstack_password] = "#{ENV['OS_PASSWORD']}"
    knife[:openstack_auth_url] = "#{ENV['OS_AUTH_URL']}"

You also have the option of passing your OpenStack API Username/Password into the individual knife subcommands using the `-A` (or `--openstack-username`) `-K` (or `--openstack-password`) command options

    # provision a new image named kb01
    knife openstack server create -A 'MyUsername' -K 'MyPassword' --openstack-api-endpoint 'http://cloud.mycompany.com:5000/v2.0/tokens' --node-name kb01 -f 1 -I 13 -S trystack -i ~/.ssh/trystack.pem -r 'role[webserver]'

Additionally the following options may be set in your `knife.rb`:

* flavor
* image
* openstack_ssh_key_id
* template_file

# Subcommands #

This plugin provides the following Knife subcommands. Specific command options can be found by invoking the subcommand with a `--help` flag

knife openstack server create
-----------------------------

Provisions a new server in an OpenStack Compute cloud and then perform a Chef bootstrap (using the SSH protocol). The goal of the bootstrap is to get Chef installed on the target system so it can run Chef Client with a Chef Server. The main assumption is a baseline OS installation exists (provided by the provisioning). It is primarily intended for Chef Client systems that talk to a Chef server. By default the server is bootstrapped using the [ubuntu10.04-gems](https://github.com/opscode/chef/blob/master/chef/lib/chef/knife/bootstrap/ubuntu10.04-gems.erb) template. This can be overridden using the `-d` or `--template-file` command options.

knife openstack server delete
-----------------------------

Deletes an existing server in the currently configured OpenStack Compute cloud account. <b>PLEASE NOTE</b> - this does not delete the associated node and client objects from the Chef server.

knife openstack server list
---------------------------

Outputs a list of all servers in the currently configured OpenStack Compute cloud account. <b>PLEASE NOTE</b> - this shows all instances associated with the account, some of which may not be currently managed by the Chef server.

knife openstack flavor list
---------------------------

Outputs a list of all available flavors (available hardware configuration for a server) available to the currently configured OpenStack Compute cloud account. Each flavor has a unique combination of virtual cpus, disk space and memory capacity. This data can be useful when choosing a flavor id to pass to the `knife openstack server create` subcommand.

knife openstack image list
--------------------------

Outputs a list of all available images available to the currently configured OpenStack Compute cloud account. An image is a collection of files used to create or rebuild a server. This data can be useful when choosing an image id to pass to the `knife openstack server create` subcommand.

# TODO #

This is a list of features currently lacking and (eventually) under development:

* need an ohai plugin to populate `cloud` and `openstack` attributes
* default flavor of '1'
* fix support for not using `-S`, by listing it in the knife.rb instead
* automate naming of nodes if `--node-name` is not passed
* take either the flavor ID or the flavor name
* take either the image ID or the image name
* add `-G` support for security groups other than 'default'
* add support for floating IPs
* more information in `knife openstack image list`
* get DNS names working
* availability zones
* test with Essex

# License #

Author:: Seth Chisamore (<schisamo@opscode.com>)

Author:: Matt Ray (<matt@opscode.com>)

Copyright:: Copyright (c) 2011-2012 Opscode, Inc.

License:: Apache License, Version 2.0

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
