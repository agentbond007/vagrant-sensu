Sensu Monitoring 
================

This GitHub repository contains a Sensu test environment, setup and configured
using Vagrant. Includes 1 monitoring server and 2 clients.

### Requirements

- Vagrant, https://www.vagrantup.com/

### Instructions

To initialize the test bed:

- Clone to local disk. `git clone https://github.com/666jfox777/vagrant-sensu.git`
- Browse to folder. `cd ~/vagrant-sensu/
- `vagrant up`

When you're done:

- `vagrant destroy`

### Customizations

You can customize the test environment through several variables exposed in the
Vagrantfile.

- `domain = "domain.com"`
- `company = "Company"`
- `monitor = "sensu.#{domain}"`
- `mailfrom = "monitoring@#{domain}"`
- `mailto = "email@#{domain}"`
- `smtpaddr = "localhost"`
- `smtpport = "25"`
- `smtpdomain = "#{domain}"`

### Testing / Alerts

To generate an alert:

- `vagrant ssh client1`
- `sudo service crond stop`

Wait a few minutes and you will see an alert displayed in the Sensu dashboard.
If you chose to configure the Vagrantfile with a valid email address you should
receive an email notification as well.

### Screenshots!

![Sensu Uchiwa Dashboard Events](screenshot-1.png?raw=true "Sensu Uchiwa Dashboard Events")
![Sensu Uchiwa Dashboard Clients](screenshot-2.png?raw=true "Sensu Uchiwa Dashboard Clients")
![Sensu Uchiwa Dashboard Client Expanded](screenshot-2.png?raw=true "Sensu Uchiwa Dashboard Client Expanded")