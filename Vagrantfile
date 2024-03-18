Vagrant.configure("2") do |config|
  config.vm.box = "generic/debian12" 
  config.vm.define "server" do |server|

    server.vm.hostname = "server.loc" 
    server.vm.network "private_network", ip: "192.168.56.10" 
    server.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
        apt install -y openvpn
        cp -r /usr/share/easy-rsa /etc/openvpn/easy-rsa
        cd /etc/openvpn/easy-rsa/
        ./easyrsa init-pki
        ./easyrsa --batch build-ca nopass
        ./easyrsa gen-dh
        echo "yes" | ./easyrsa build-server-full server nopass
        echo "yes" | ./easyrsa build-client-full client nopass
        cp -pR ./pki/{issued/server.crt,private/server.key,ca.crt,dh.pem} /etc/openvpn/
        cp -pR ./pki/{issued/client.crt,private/client.key,ca.crt} /etc/openvpn/client/
    SHELL

  end 
  config.vm.define "client" do |client| 

    client.vm.hostname = "client.loc" 
    client.vm.network "private_network", ip: "192.168.56.20"
    client.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
        apt install -y openvpn
      SHELL
    client.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/playbook.yml"
      ansible.inventory_path = "ansible/hosts"
      ansible.host_key_checking = "false"
      ansible.limit = "all"
    end
  end
end

