IMAGE_NAME = "debian/buster64"
N = 2

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false

  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.cpus = 2
  end
    
  config.vm.define "hadoop-controller" do |master|
    master.vm.box = IMAGE_NAME
    master.vm.network "private_network", ip: "192.168.50.10"
    master.vm.hostname = "hadoop-controller"

    master.vm.provision "ansible_hadoop", type:"ansible" do |ansible|
      ansible.playbook = "playbooks/install_hadoop.yml"
      ansible.extra_vars = {
        "controller_ip" => "192.168.50.10",
        "node1_ip" => "192.168.50.11",
        "node2_ip" => "192.168.50.12",
      }
    end

    master.vm.provision "ansible_spark", type:"ansible" do |ansible|
      ansible.playbook = "playbooks/install_spark.yml"
    end
  end

  (1..N).each do |i|
    config.vm.define "hadoop-node-#{i}" do |node|
      node.vm.box = IMAGE_NAME
      node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
      node.vm.hostname = "node-#{i}"

      node.vm.provision "ansible_hadoop", type:"ansible" do |ansible|
        ansible.playbook = "playbooks/install_hadoop.yml"
        ansible.extra_vars = {
          "controller_ip" => "192.168.50.10",
          "node1_ip" => "192.168.50.11",
          "node2_ip" => "192.168.50.12",
        }
      end
    end
  end
end