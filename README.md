# Puppet module: dnsmasq

## [puppet-dnsmasq](https://github.com/procore/puppet-dnsmasq) is now being maintained by Procore as of December 2016. Feel free to submit any issues at: https://github.com/procore/puppet-dnsmasq/issues or open PRs and join us.

Thank you

## Getting started

This is a Puppet module for dnsmasq based on the second generation layout ("NextGen") of Example42 Puppet Modules.

Made by Javier Bértoli / Netmanagers

Official site: http://www.netmanagers.com.ar

Official git repository: http://github.com/procore/puppet-dnsmasq

Released under the terms of Apache 2 License.

This module depends on R.I.Pienaar's concat module (https://github.com/ripienaar/puppet-concat).

This module requires functions provided by the Example42 Puppi module (you need it even if you don't
use and install Puppi)

For detailed info about the logic and usage patterns of Example42 modules check the DOCS directory on Example42 main modules set.


## USAGE - Basic management

* All parameters can be set using Hiera. See the manifests to see what can be set.

* dnsmasq has a gazillion parameters available, so please take a look to init.pp, params.pp and the templates, to see what can be done with this module. 

* Many configuration parameters in dnsmasq can be specified more than once, and even with different structure in the values assigned:

       domain=reserved.thekelleys.org.uk,192.68.3.100,192.168.3.200
       domain=example.com

       dhcp-range=1234::, ra-only 
       dhcp-range=1234::2, 1234::500, 64, 12h

  so these variables can be passed as arrays with a string as value, and they will be set as many
times as needed in the config file, all hyphens (-) given as underscores (_):

        class { 'dnsmasq': 
          domain     => [ 'reserved.thekelleys.org.uk,192.68.3.100,192.168.3.200', 
                          'example.com' ]
          dhcp_range => [ '1234::, ra-only',
                          '1234::2, 1234::500, 64, 12h' ] 
        }

* As I like to have extra hosts added in the directory "/etc/hosts.d/", with different files holding
records with different criteria, I've created a define "dnsmasq::addhost" that uses "concat" and 
creates these files as needed:

        dnsmasq::addhost {
          ip    => '111.222.333.444',
          names = ['onename.example.com', 
                   'anothername.example.com'],
          file = 'example.com.hosts',
        }

   This will add an entry in the file "/etc/hosts.d/50-example.com.hosts"

        111.222.333.444 onename.example.com anothername.example.com

and it will be added to the list of files to check in "addn-hosts"

* Install dnsmasq with default settings

        class { 'dnsmasq': }

* Install a specific version of dnsmasq package

* Install a specific version of fail2ban package

        class { 'fail2ban':
        class { 'dnsmasq':
          version => '1.0.1',
        }

* Disable dnsmasq service.

        class { 'dnsmasq':
          disable => true
        }

* Remove dnsmasq package

        class { 'dnsmasq':
          absent => true
        }

* Enable auditing without without making changes on existing dnsmasq configuration *files*

        class { 'dnsmasq':
          audit_only => true
        }

* Module dry-run: Do not make any change on *all* the resources provided by the module

        class { 'dnsmasq':
          noops => true
        }


## USAGE - Overrides and Customizations
* Use custom sources for main config file 

        class { 'dnsmasq':
          source => [ "puppet:///modules/example42/dnsmasq/dnsmasq.conf-${hostname}" , "puppet:///modules/example42/dnsmasq/dnsmasq.conf" ], 
        }


* Use custom source directory for the whole configuration dir

        class { 'dnsmasq':
          source_dir       => 'puppet:///modules/example42/dnsmasq/conf/',
          source_dir_purge => false, # Set to true to purge any existing file not present in $source_dir
        }

* Use custom template for main config file. Note that template and source arguments are alternative. 

        class { 'dnsmasq':
          template => 'example42/dnsmasq/dnsmasq.conf.erb',
        }

* Automatically include a custom subclass

        class { 'dnsmasq':
          my_class => 'example42::my_dnsmasq',
        }


## USAGE - Example42 extensions management 
* Activate puppi (recommended, but disabled by default)

        class { 'dnsmasq':
          puppi    => true,
        }

* Activate puppi and use a custom puppi_helper template (to be provided separately with a puppi::helper define ) to customize the output of puppi commands 

        class { 'dnsmasq':
          puppi        => true,
          puppi_helper => 'myhelper', 
        }

* Activate automatic monitoring (recommended, but disabled by default). This option requires the usage of Example42 monitor and relevant monitor tools modules

        class { 'dnsmasq':
          monitor      => true,
          monitor_tool => [ 'nagios' , 'monit' , 'munin' ],
        }

* Activate automatic firewalling. This option requires the usage of Example42 firewall and relevant firewall tools modules

        class { 'dnsmasq':       
          firewall      => true,
          firewall_tool => 'iptables',
          firewall_src  => '10.42.0.0/24',
          firewall_dst  => $ipaddress_eth0,
        }


## CONTINUOUS TESTING

Travis {<img src="https://travis-ci.org/procore/puppet-dnsmasq.png?branch=master" alt="Build Status" />}[https://travis-ci.org/procore/puppet-dnsmasq]
