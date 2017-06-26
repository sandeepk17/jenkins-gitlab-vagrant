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
  config.vm.box = "bento/ubuntu-16.04"

  config.vm.network "forwarded_port", guest: 80, host: 8080

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
echo Create gitlab sources
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash
echo Create Jenkins sources
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | apt-key add -
echo -e '\ndeb https://pkg.jenkins.io/debian-stable binary/' >> /etc/apt/sources.list
apt-get update
apt-get install gitlab-ce -y
apt-get install jenkins -y
sed -i 's/8080/8090/g' /etc/default/jenkins
sed -ri 's/^JENKINS_ARG/#JENKINS_ARGS/g' /etc/default/jenkins
echo -e '\nJENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpPort=$HTTP_PORT --prefix=$PREFIX"' >> /etc/default/jenkins
service jenkins restart
gitlab-ctl reconfigure
sed -i '$d' /var/opt/gitlab/nginx/conf/gitlab-http.conf
echo -e '  location /jenkins {\n    proxy_cache off;\n    proxy_pass http://127.0.0.1:8090;\n  }\n}' >> /var/opt/gitlab/nginx/conf/gitlab-http.conf
gitlab-ctl restart
apt-get install maven -y
  SHELL

end
