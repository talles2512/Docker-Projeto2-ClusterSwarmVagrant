master = {"hostname" => "master", "memory" => "1024", "cpu" => "1", "image" => "bento/ubuntu-22.04", "ip" => "192.168.15.30"}

workers = [
  {"hostname" => "node01", "memory" => "1024", "cpu" => "1", "image" => "bento/ubuntu-22.04", "ip" => "192.168.15.31"},
  {"hostname" => "node02", "memory" => "1024", "cpu" => "1", "image" => "bento/ubuntu-22.04", "ip" => "192.168.15.32"},
  {"hostname" => "node03", "memory" => "1024", "cpu" => "1", "image" => "bento/ubuntu-22.04", "ip" => "192.168.15.33"}
]

Vagrant.configure("2") do |config|
  
  # definindo master
  config.vm.define "#{master["hostname"]}" do |machine|
    machine.vm.hostname = "#{master["hostname"]}"
    machine.vm.box = "#{master["image"]}"
    machine.vm.network "public_network", ip: "#{master["ip"]}"
    machine.vm.provider "virtualbox" do |v|
      v.name = "#{master["hostname"]}"
      v.memory = master["memory"]
      v.cpus = master["cpu"]
    end
    machine.vm.provision "shell", path: "./provision.sh"
    machine.vm.provision "shell", inline: "docker swarm init --advertise-addr #{master["ip"]}"
    machine.vm.provision "shell", inline: "docker swarm join-token -q worker > /vagrant/token"
  end

  # definindo workers
  workers.each do |worker|
    config.vm.define "#{worker["hostname"]}" do |machine|
      machine.vm.hostname = "#{worker["hostname"]}"
      machine.vm.box = "#{worker["image"]}"
      machine.vm.network "public_network", ip: "#{worker["ip"]}"
      machine.vm.provider "virtualbox" do |v|
        v.name = "#{worker["hostname"]}"
        v.memory = worker["memory"]
        v.cpus = worker["cpu"]
      end
      machine.vm.provision "shell", path: "./provision.sh"
      machine.vm.provision "shell", inline: "docker swarm join --token `cat /vagrant/token` #{master["ip"]}:2377"
    end
  end
end
