# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "bento/centos-7.3"

#  config.vm.network "forwarded_port", guest: 80, host: 8080

  config.vm.provider :vmware_workstation do |v|
    v.vmx["memsize"] = "4096"
    v.vmx["numvcpus"] = "2"
    v.vmx["ethernet0.pcislotnumber"] = "32"
  end

  config.vm.provider :virtualbox do |v|
    v.memory = 4096
    v.cpus = 2
  end

  config.vm.provision "shell", inline: <<-SHELL
sudo yum install epel-release -y 
sudo yum install ansible -y

sudo yum install wget -y
wget https://bootstrap.pypa.io/get-pip.py
sudo python get-pip.py
sudo yum install python-devel -y
sudo pip install pyvmomi

sudo yum install java-1.8.0-openjdk.x86_64 -y
sudo cp /etc/profile /etc/profile_backup
echo 'export JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk' | sudo tee -a /etc/profile
echo 'export JRE_HOME=/usr/lib/jvm/jre' | sudo tee -a /etc/profile
source /etc/profile

sudo yum install wget -y
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
sudo rpm --import http://pkg.jenkins-ci.org/redhat-stable/jenkins-ci.org.key
sudo yum install jenkins -y
sudo sed -i 's/8080/8090/g' /etc/sysconfig/jenkins
sudo sed -ri 's/^JENKINS_ARGS/#JENKINS_ARGS/g' /etc/sysconfig/jenkins
echo -e '\nJENKINS_ARGS="--prefix=/jenkins"' | sudo tee -a /etc/sysconfig/jenkins
sudo systemctl start jenkins.service
sudo systemctl enable jenkins.service

curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
sudo yum install gitlab-ce -y
sudo gitlab-ctl reconfigure
sudo sed -i '$d' /var/opt/gitlab/nginx/conf/gitlab-http.conf
echo -e '  location /jenkins {\n    proxy_cache off;\n    proxy_pass http://127.0.0.1:8090;\n  }\n}' | sudo tee -a /var/opt/gitlab/nginx/conf/gitlab-http.conf
sudo gitlab-ctl restart

sudo yum install maven -y

#sudo firewall-cmd --permanent --add-service=http
#sudo systemctl reload firewalld
  SHELL

end
