# -*- mode: ruby -*-
# vi: set ft=ruby :
#
#Vagrant.configure("1") do |config|
#  config.vm.boot_mode = :gui
#end

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

require 'json'
configfile = File.read(File.dirname(__FILE__) + '/vagrant.json')
CONFIG = JSON.parse(configfile)
print "Reading " + File.dirname(__FILE__) + '/vagrant.json', "\n"


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
        
    CONFIG["hosts"].each do |hostname,hostdef|
        #puts "Configuring #{hostname}"
	  	config.vm.define hostname  do |host|
            host.vm.box = CONFIG["hosts"][hostname]["box"]
	        host_name = "#{CONFIG['envname']}-#{hostname}-#{CONFIG['site']}.#{CONFIG['netname']}"
            ip1 = "#{CONFIG['netsegment']}.#{CONFIG["hosts"][hostname]["ip_address1"]}"
            ip2 = "#{CONFIG['netsegment']}.#{CONFIG["hosts"][hostname]["ip_address2"]}"

	        host.vm.host_name = host_name
	        #host.vm.network :private_network, ip: ip1, :adapter => 2, auto_config: true
	        #host.vm.network :private_network, ip: ip2, :adapter => 3, auto_config: true
	        host.vm.network :private_network, ip: ip1, :adapter => 2, auto_config: false
	        host.vm.network :private_network, ip: ip2, :adapter => 3, auto_config: false
	        #host.vm.network :private_network, type: "dhcp", :adapter => 4 
            

            #config.vm.network "forwarded_port", guest: 80, host: 8081, auto_correct: true
            #config.vm.network "forwarded_port", guest: 443, host: 8444, auto_correct: true

            #host.vm.network :forwarded_port, :guest => 80, host => 8080, auto_correct: true
            #host.vm.network :forwarded_port, :guest => 443, host => 4443, auto_correct: true
            #
            #host.vm.network "forwarded_port", :guest => 80, host => 8080, auto_correct: true
            #host.vm.network "forwarded_port", :guest => 443, host => 4443, auto_correct: true
	

	        host.vm.provider :virtualbox do |vb|
                host_memory = CONFIG["hosts"][hostname]["memory"]
	            vb.customize ["modifyvm", :id,"--memory", CONFIG["hosts"][hostname]["memory"]]
                vb.customize ["modifyvm", :id,"--usb","off"]
		        portno = 1
                #puts "Number of disks for #{hostname}: #{CONFIG['hosts'][hostname]['disks']}"
                #puts "Number of disks for #{hostname}: #{CONFIG['hosts'][hostname]['disks']}"
                (1..CONFIG["hosts"][hostname]["disks"]).each do |d|
	                portno += 1
	                disk = File.dirname(__FILE__) + "/disks/" + "#{CONFIG['hosts']['envname']}" + "#{hostname}" + "#{d}" + ".vdi"
                    #puts "making disk #{disk}"
	                if File.exist?(disk)
		                vb.customize ['storageattach',:id,'--storagectl','SATA','--port',portno,'--device',0,'--type','hdd','--mtype','shareable','--medium',disk ]
	                else
		                vb.customize ['createhd','--filename',disk,'--size',CONFIG['hosts'][hostname]['disksize'],'--format','VDI','--variant','Fixed']
	                    vb.customize ['storageattach',:id,'--storagectl','SATA','--port',portno,'--device',0,'--type','hdd','--mtype','shareable','--medium',disk ]
	                end
	            end
            end #End of host.vm.provider
	            
		    config.vm.provision :shell, :inline =>  "usermod -s /bin/zsh vagrant"
		    config.vm.provision :shell, :inline =>  "cp /vagrant/etc/_vimrc /home/vagrant/.vimrc"
		    config.vm.provision :shell, :inline =>  "chown vagrant:vagrant /home/vagrant/.vimrc"
		    config.vm.provision :shell, :inline =>  "cp /vagrant/etc/_zshrc /home/vagrant/.zshrc"
		    config.vm.provision :shell, :inline =>  "chown vagrant:vagrant /home/vagrant/.zshrc"

	
	        echostring1 = "#{CONFIG['netsegment']}.101  #{CONFIG['envname']}-ns1-1-ltm.#{CONFIG['netname']}   #{CONFIG['envname']}-ns1-1-ltm   ns1"
	        echostring2 = "#{CONFIG['netsegment']}.43   #{CONFIG['envname']}-ctlr1-1-ltm.#{CONFIG['netname']} #{CONFIG['envname']}-ctlr1-1-ltm ctlr1"
	        echostring3 = "#{CONFIG['netsegment']}.63   #{CONFIG['envname']}-inst1-1-ltm.#{CONFIG['netname']} #{CONFIG['envname']}-inst1-1-ltm inst1"
	        
	        config.vm.provision :shell, :inline => "echo #{echostring1} >> /etc/hosts"
	        config.vm.provision :shell, :inline => "echo #{echostring2} >> /etc/hosts"
	        config.vm.provision :shell, :inline => "echo #{echostring3} >> /etc/hosts"


            config.vm.provision :shell, :inline => "/bin/rpm --import /vagrant/RPMS/RPM-GPG-KEY-puppet"
            config.vm.provision :shell, :inline => "/usr/bin/yum install -y puppet"
            config.vm.provision :shell, :inline => "/bin/cp /vagrant/etc/puppet/puppet.conf /etc/puppetlabs/puppet/puppet.conf"

        end #End of config.vm.define
    end #End of CONFIG["hosts"].each

end
