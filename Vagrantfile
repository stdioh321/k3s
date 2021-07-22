Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo Outside VMs"
  
  config.vm.define "kubmaster" do |web|
    web.vm.box = "hashicorp/bionic64"
    web.vm.hostname = "kubmaster"
    web.vm.synced_folder '.', "/root"
    
    web.vm.provider :virtualbox do |vb|
      vb.name = "kubmaster"
      vb.gui = false
      # vb.memory = 2048
      # vb.cpus = 3
    end
    # web.vm.provision "shell", inline: "rm /root/node-token"
    web.vm.provision "shell", inline: "apt update && apt install -y curl"
    web.vm.provision "shell", inline: "curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC='--node-ip 10.10.10.10' K3S_NODE_NAME='kubmaster' K3S_KUBECONFIG_MODE='644' sh -"
    web.vm.provision "shell", inline: "cp /var/lib/rancher/k3s/server/node-token /root"
    # web.vm.provision "shell", inline: "kubectl apply -f /root/nginx.yaml"
    # web.vm.provision "shell", inline: "kubectl apply -f /root/nginx.yaml"
   
    
    web.vm.network "private_network", ip: "10.10.10.10"
  end
  (1..2).each do |i|
    config.vm.define "worker#{i}" do |web|
      web.vm.box = "hashicorp/bionic64"
      web.vm.hostname = "worker#{i}"
      web.vm.synced_folder '.', "/root"
      web.vm.provider :virtualbox do |vb|
        vb.name = "worker#{i}"
        vb.gui = false
        # vb.memory = 2048
        # vb.cpus = 3
      end
      web.vm.provision "shell", inline: "apt update && apt install -y curl"
      web.vm.provision "shell", inline: "curl -sfL https://get.k3s.io | K3S_URL=https://10.10.10.10:6443 K3S_NODE_NAME=worker#{i} K3S_TOKEN=`head -1 /root/node-token` sh -"
      web.vm.provision "shell", inline: "systemctl stop k3s-agent.service && systemctl start k3s-agent.service"
  
      web.vm.network "private_network", ip: "10.10.10.1#{i}"        
    end
  end 
  
end