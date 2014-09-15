# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"

domain = "domain.com"
company = "Company"
monitor = "sensu.#{domain}"
mailfrom = "monitoring@#{domain}"
mailto = "email@#{domain}"
smtpaddr = "localhost"
smtpport = "25"
smtpdomain = "#{domain}"
clients = 2

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "nrel/CentOS-6.5-x86_64"
  config.vm.provider "virtualbox" do |v|
    v.memory = 512
    v.cpus = 1
  end

  config.vm.define "sensu" do |sensu|
    sensu.vm.host_name = "sensu"
    sensu.vm.network "private_network", ip: "192.168.250.100"
    sensu.vm.network "forwarded_port", guest: 3000, host: 3000
    sensu.vm.provision "shell", inline: "sudo bash"
    sensu.vm.provision "shell", inline: "service iptables stop"
    sensu.vm.provision "shell", inline: "rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm"
    sensu.vm.provision "shell", inline: "yum install erlang -y"
    sensu.vm.provision "shell", inline: "rpm --import http://www.rabbitmq.com/rabbitmq-signing-key-public.asc"
    sensu.vm.provision "shell", inline: "rpm -Uvh http://www.rabbitmq.com/releases/rabbitmq-server/v3.2.1/rabbitmq-server-3.2.1-1.noarch.rpm"
    sensu.vm.provision "shell", inline: "chkconfig rabbitmq-server on"
    sensu.vm.provision "shell", inline: "/etc/init.d/rabbitmq-server start"
    sensu.vm.provision "shell", inline: "cp -rf /vagrant/server/rabbitmq/* /etc/rabbitmq/"
    sensu.vm.provision "shell", inline: "/etc/init.d/rabbitmq-server restart"
    sensu.vm.provision "shell", inline: "rabbitmqctl add_vhost /sensu"
    sensu.vm.provision "shell", inline: "rabbitmqctl add_user sensu mypass"
    sensu.vm.provision "shell", inline: "rabbitmqctl set_permissions -p /sensu sensu '.*' '.*' '.*'"
    sensu.vm.provision "shell", inline: "yum install redis -y"
    sensu.vm.provision "shell", inline: "/sbin/chkconfig redis on"
    sensu.vm.provision "shell", inline: "/etc/init.d/redis start"
    sensu.vm.provision "shell", inline: "cp -f /vagrant/server/sensu.repo /etc/yum.repos.d/"
    sensu.vm.provision "shell", inline: "yum install sensu uchiwa -y"
    sensu.vm.provision "shell", inline: "yum remove ruby -y"
    sensu.vm.provision "shell", inline: "echo 'EMBEDDED_RUBY=true' > /etc/default/sensu"
    sensu.vm.provision "shell", inline: "ln -s /opt/sensu/embedded/bin/ruby /usr/bin/"
    sensu.vm.provision "shell", inline: "/opt/sensu/embedded/bin/gem install mail --no-rdoc --no-ri"
    sensu.vm.provision "shell", inline: "cp -rf /vagrant/server/sensu/* /etc/sensu/"
    sensu.vm.provision "shell", inline: "sed -i 's/clientname/#{monitor}/g' /etc/sensu/conf.d/client.json"
    sensu.vm.provision "shell", inline: "sed -i 's/ipaddr/192.168.250.100/g' /etc/sensu/conf.d/client.json"
    sensu.vm.provision "shell", inline: "sed -i 's/sensu/#{monitor}/g' /etc/sensu/conf.d/mailer.json"
    sensu.vm.provision "shell", inline: "sed -i 's/mailfrom/#{mailfrom}/g' /etc/sensu/conf.d/mailer.json"
    sensu.vm.provision "shell", inline: "sed -i 's/mailto/#{mailto}/g' /etc/sensu/conf.d/mailer.json"
    sensu.vm.provision "shell", inline: "sed -i 's/smtpaddr/#{smtpaddr}/g' /etc/sensu/conf.d/mailer.json"
    sensu.vm.provision "shell", inline: "sed -i 's/smtpport/#{smtpport}/g' /etc/sensu/conf.d/mailer.json"
    sensu.vm.provision "shell", inline: "sed -i 's/smtpdomain/#{smtpdomain}/g' /etc/sensu/conf.d/mailer.json"
    sensu.vm.provision "shell", inline: "chkconfig sensu-server on"
    sensu.vm.provision "shell", inline: "chkconfig sensu-client on"
    sensu.vm.provision "shell", inline: "chkconfig sensu-api on"
    sensu.vm.provision "shell", inline: "chkconfig uchiwa on"
    sensu.vm.provision "shell", inline: "service sensu-server start"
    sensu.vm.provision "shell", inline: "service sensu-api start"
    sensu.vm.provision "shell", inline: "service sensu-client start"
    sensu.vm.provision "shell", inline: "service uchiwa start"
  end

  for i in 1..clients
    config.vm.define "client#{i}" do |client|
      client.vm.host_name = "client#{i}"
      client.vm.network "private_network", ip: "192.168.250.10#{i}"
      client.vm.provision "shell", inline: "sudo bash"
      client.vm.provision "shell", inline: "service iptables stop"
      client.vm.provision "shell", inline: "cp -f /vagrant/client/sensu.repo /etc/yum.repos.d/"
      client.vm.provision "shell", inline: "yum install sensu -y"
      client.vm.provision "shell", inline: "yum remove ruby -y"
      client.vm.provision "shell", inline: "echo 'EMBEDDED_RUBY=true' > /etc/default/sensu"
      client.vm.provision "shell", inline: "ln -s /opt/sensu/embedded/bin/ruby /usr/bin/"
      client.vm.provision "shell", inline: "cp -rf /vagrant/client/sensu/* /etc/sensu/"
      client.vm.provision "shell", inline: "sed -i 's/clientname/client.#{i}.#{domain}/g' /etc/sensu/conf.d/client.json"
      client.vm.provision "shell", inline: "sed -i 's/ipaddr/192.168.250.10#{i}/g' /etc/sensu/conf.d/client.json"
      client.vm.provision "shell", inline: "chkconfig sensu-client on"
      client.vm.provision "shell", inline: "service sensu-client start"
    end
  end

end
