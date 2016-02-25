# tomcat Cookbook
[![Build Status](https://travis-ci.org/chef-cookbooks/tomcat.svg?branch=master)](https://travis-ci.org/chef-cookbooks/tomcat) [![Cookbook Version](https://img.shields.io/cookbook/v/tomcat.svg)](https://supermarket.chef.io/cookbooks/tomcat)

Installs and configures Tomcat, Java servlet engine and webserver.

## Note on the Recent Rewrite
This cookbook features a newly written set of custom resources for installing Tomcat, as well as a legacy attributes driven recipe / provider combination. The recipe / provider attempted to manage everything Tomcat, which is nearly impossible given the breadth of the Tomcat XML configs. In this attempt to be everything to everyone, we were unfortunately excellent to no one. In the near future the recipes and instance provider will be removed in favor of the simpler tomcat_install and tomcat_service custom resources.  These resources add support for modern Tomcat releases and function better in the wrapper cookbook / library model that complex applications like Tomcat lend themselves to.  For the 2.0 release the attributes, recipes, and instance provider will stay in place, but documentation for those methods will be removed.  For the 3.0 release they will be removed entirely. See the Usage section below for more information on how to use the custom resources with a wrapper cookbook.

## Requirements
### Platforms
- Debian / Ubuntu derivatives
- RHEL derivatives
- Fedora
- openSUSE / SUSE Linux Enterprises

### Chef
- Chef 12.1+

### Cookbooks
- java
- openssl
- yum-epel
- compat_resource

## Custom Resources
This cookbook is currently undergoing a ground up rewrite that will convert it to a pure library cookbook, more appropriate for the multitude of ways that Tomcat can be installed.  The existing attribute driven installs and tomcat_instance provider will eventually be deprecated in favor of a provider for installation, service management, and 1 or more providers for configuration.

### tomcat_install
tomcat_install installs an instance of the tomcat binary direct from Apache's mirror site. As distro packages are not used we can easily deploy per-instance installations and any version available on the Apache archive site can be installed.

#### properties
- `version`: The version to install. Default: 8.0.32
- `path`: Full path to the install directory. Default: /opt/tomcat_INSTANCENAME_VERSION
- `tarball_base_path`: The base path to the apache mirror containing the tarballs. Default: '[http://archive.apache.org/dist/tomcat/](http://archive.apache.org/dist/tomcat/)'
- `sha1_base_path`: The base path to the apache mirror containing the sha1 file. Default: '[http://archive.apache.org/dist/tomcat/](http://archive.apache.org/dist/tomcat/)'
- `exclude_docs`: Exclude ./webapps/docs from installation. Default true.
- `exclude_examples`: Exclude ./webapps/examples from installation. Default true.
- `exclude_manager`: Exclude ./webapps/manager from installation. Default: false.
- `exclude_host-manager`: Exclude ./webapps/host-manager from installation. Default: false.

#### example
Install an Tomcat 8.0.32 instance named 'helloworld' to /opt/tomcat_helloworld_8_0_32/ with a symlink at /opt/tomcat_helloworld/

```ruby
tomcat_install 'helloworld' do
  version '8.0.32'
end
```

### tomcat_service
tomcat_service sets up the installed tomcat instance to run using the appropriate init system (sys-v, upstart, or systemd)

#### properties
- `path`: Full path to the install directory. Default: /opt/tomcat_INSTANCENAME
- `env_vars`: An array of hashes containing the environmental variables for Tomcat's setenv.sh script. Note: If CATALINA_BASE is not passed it will automatically be added as the first item in the array. Default: [ {'CATALINA_BASE' => '/opt/INSTANCE_NAME/'}, {'CATALINA_PID' => '$CATALINA_BASE/bin/tomcat.pid'} ]

#### actions
- `start`
- `stop`
- `disable`
- `restart`

#### example

```ruby
tomcat_service 'helloworld' do
  action :start
  env_vars [{CATALINA_PID => '/my/special/path/tomcat.pid'}]
end
```

## License & Authors
- Author: Community Cookbook Team ([cookbooks@chef.io](mailto:cookbooks@chef.io))

```text
Copyright:: 2010-2015, Chef Software, Inc

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
