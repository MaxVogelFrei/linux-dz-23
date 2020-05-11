# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :web => {
        :box_name => "centos/7",
        :ip_addr => '192.168.23.150'
  }
}
Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

      config.vm.define boxname do |box|

          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s

          box.vm.network "forwarded_port", guest: 8080, host: 8080
          box.vm.network "forwarded_port", guest: 8081, host: 8081
          box.vm.network "forwarded_port", guest: 8082, host: 8082
          box.vm.network "forwarded_port", guest: 8083, host: 8083

          box.vm.network "private_network", ip: boxconfig[:ip_addr]

          box.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "1024"]
          end
          box.vm.provision "shell", inline: <<-SHELL
            mkdir -p ~root/.ssh; cp ~vagrant/.ssh/auth* ~root/.ssh
            sed -i '65s/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
            systemctl restart sshd
          SHELL
          box.vm.provision "ansible" do |ansible|
              ansible.compatibility_mode = "2.0"
              ansible.playbook = "web.yml"
              ansible.verbose = "true"
              ansible.become = "true"
              ansible.limit = "all"
          end
      end
  end
end
