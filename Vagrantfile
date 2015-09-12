require "yaml"
app_config = YAML::load_file("./.project_name.yml")['parameters'];

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.network :private_network, ip: "10.0.0.208"
  config.vm.network "public_network"
  config.ssh.forward_agent = true

  config.vm.provider :virtualbox do |v|
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--memory", 1536]
    v.customize ["modifyvm", :id, "--name", app_config["project_name"]]
    v.customize ["modifyvm", :id, "--cpuexecutioncap", "90"]
    v.cpus = 2
  end


  nfs_setting = RUBY_PLATFORM =~ /darwin/ || RUBY_PLATFORM =~ /linux/
  config.vm.synced_folder "./../", "/var/www/" + app_config["project_name"],
    id: "vagrant-root" , owner: "vagrant", group: "vagrant"

  config.vm.provision "shell",
      inline: "
        sudo apt-get --yes install software-properties-common python-pip > /dev/null 2>&1;
        sudo apt-add-repository --yes ppa:ansible/ansible > /dev/null 2>&1;
        sudo apt-get --yes update  > /dev/null 2>&1;
        sudo apt-get --yes install ansible"

  config.vm.provision "shell",
    inline: "cp -R /vagrant ~/;
             chmod -x ~/vagrant/provisioning/hosts;
             cd ~/vagrant/provisioning;
             ansible-playbook -i hosts site.yml;"

end
