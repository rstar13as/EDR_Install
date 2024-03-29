Vagrant.configure("2") do |config|

  config.vm.box = "generic/centos8"
  config.vm.hostname = "cbresponse"
  config.vm.network :private_network, ip: "192.168.193.141"

  # Make sure you save the cbinit inforamtion inside cbini.ini 
  # Put the CB EDR rpm package into the same folder. 
  # Request them from your SE.

  config.vm.provision "file", source: "cbinit.ini", destination: "cbinit.ini"
  config.vm.provision "file", source: "carbon-black-release-1.0.4-1-Carbon Black Inc._I149104.x86_64.rpm", destination: "carbon-black-release-1.0.4-1-Carbon Black Inc._I149104.x86_64.rpm"

# Configure provider-specific settings for VMware Workstation
  
  config.vm.provider "vmware_workstation" do |vmw|
    # Set the amount of memory (in MB)
    vmw.vmx["memsize"] = "32768"  # 32GB in MB

    # Set the number of CPUs
    vmw.vmx["numvcpus"] = "2"

    # Set the number of cores per CPU
    vmw.vmx["coresPerSocket"] = "2"
  end

config.vm.provision "shell", inline: <<-SHELL
    echo "192.168.193.141     cbresponse cbresponse.local" | sudo tee -a /etc/hosts
    sudo hostnamectl set-hostname cbresponse
    sudo systemctl restart systemd-hostnamed
    sudo timedatectl set-ntp true
    sudo rpm -ivh 'carbon-black-release-1.0.4-1-Carbon Black Inc._I149104.x86_64.rpm'
    sudo yum -y module disable postgresql redis python38 python39
    sudo yum -y install cb-enterprise 
    sudo /usr/share/cb/cbinit cbinit.ini
    sudo /usr/share/cb/cbssl backup --out backup.bac
    sudo service cb-enterprise start
    curl --insecure -I https://192.168.193.141:443 | grep HTTP
    curl --insecure -s --head https://192.168.193.141:443 | head -n 1
    echo "https://$(hostname -I | awk '{print $1}'):443"
  SHELL
end
