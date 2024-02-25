MACHINES = {
  :"system-d" => {
             :box_name => "generic/centos8s",
             :box_version => "4.3.4",
             :cpus => 2,
             :memory => 4096,
            }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.box_version = boxconfig[:box_version]
      box.vm.host_name = boxname.to_s
      box.vm.provider "virtualbox" do |v|
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
      end
      box.vm.provision "shell", inline: <<-SHELL
        yum install -y wget
        yum install -y epel-release && yum install -y spawn-fcgi php mod_fcgid
        wget -P /etc/sysconfig/ https://raw.githubusercontent.com/olegizt/DZ9_SystemD/main/watchlog
        touch /var/log/watchlog.log
        wget -P /opt/ https://raw.githubusercontent.com/olegizt/DZ9_SystemD/main/watchlog.sh
        chmod +x /opt/watchlog.sh
        wget -P /etc/systemd/system/ https://raw.githubusercontent.com/olegizt/DZ9_SystemD/main/watchlog.service
        wget -P /etc/systemd/system/ https://raw.githubusercontent.com/olegizt/DZ9_SystemD/main/watchlog.timer
        systemctl start watchlog.timer
        rm /etc/sysconfig/spawn-fcgi
        wget -P /etc/sysconfig/ https://raw.githubusercontent.com/olegizt/DZ9_SystemD/main/spawn-fcgi
        wget -P /etc/systemd/system/ https://raw.githubusercontent.com/olegizt/DZ9_SystemD/main/spawn-fcgi.service
        systemctl start spawn-fcgi
#        systemctl enable spawn-fcgi
        wget -P /etc/sysconfig/ https://raw.githubusercontent.com/olegizt/DZ9_SystemD/main/httpd-first
        wget -P /etc/sysconfig/ https://raw.githubusercontent.com/olegizt/DZ9_SystemD/main/httpd-second
        wget -P /etc/httpd/conf/ https://raw.githubusercontent.com/olegizt/DZ9_SystemD/main/first.conf
        wget -P /etc/httpd/conf/ https://raw.githubusercontent.com/olegizt/DZ9_SystemD/main/second.conf
        systemctl start httpd@first httpd@second
#        systemctl enable httpd@first httpd@second

        echo '#!/bin/bash' >> /usr/local/sbin/motd.sh
        chmod +x /usr/local/sbin/motd.sh
        echo 'printf "\e[0;37;42mПривет! Проверка настроек ДЗ №9 - Инициализация системы. Systemd.\nВыполните в консоле следующие команды для проверки корректности выполненной работы:\nsudo tail -f /var/log/messages\nsudo systemctl status spawn-fcgi\nsudo ss -tnulp | grep httpd\e[0m\n"' >> /usr/local/sbin/motd.sh
        echo 'sudo systemctl start watchlog.service' >> /usr/local/sbin/motd.sh
        echo "PrintMotd no" >> /etc/ssh/sshd_config
        systemctl restart sshd
        echo '/usr/local/sbin/motd.sh' >> /etc/profile
      SHELL
    end
  end
end
