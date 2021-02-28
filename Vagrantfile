IMAGE_NAME = "debian/buster64"
CONTROLLER_NODE_NAME = "hadoop-controller-node"
WORKER_NODE_PREFIX = "hadoop-node"
N = 2

$script = <<-SCRIPT
sudo su hdfs
/usr/local/hadoop-3.3.0/bin/hdfs namenode -format
SCRIPT

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false

  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.cpus = 2
  end

  (1..N).each do |i|
    config.vm.define "#{WORKER_NODE_PREFIX}-#{i}" do |node|
      node.vm.box = IMAGE_NAME
      node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
      node.vm.hostname = "#{WORKER_NODE_PREFIX}-#{i}"

      node.vm.provision "ansible_hadoop", type:"ansible" do |ansible|
        ansible.playbook = "playbooks/install_hadoop.yml"
        ansible.extra_vars = {
          "controller_node_ip" => "192.168.50.10",
          "node_1_ip" => "192.168.50.11",
          "node_2_ip" => "192.168.50.12",
        }
      end
    end
  end

  config.vm.define "#{CONTROLLER_NODE_NAME}" do |master|
    master.vm.box = IMAGE_NAME
    master.vm.network "private_network", ip: "192.168.50.10"
    master.vm.hostname = "#{CONTROLLER_NODE_NAME}"

    master.vm.provision "ansible_hadoop", type:"ansible" do |ansible|
      ansible.playbook = "playbooks/install_hadoop.yml"
      ansible.extra_vars = {
        "controller_node_ip" => "192.168.50.10",
        "node_1_ip" => "192.168.50.11",
        "node_2_ip" => "192.168.50.12",
      }
    end

    master.vm.provision "ansible_spark", type:"ansible" do |ansible|
      ansible.playbook = "playbooks/install_spark.yml"
    end

    master.vm.provision "ansible_services", type:"ansible" do |ansible|
      ansible.playbook = "playbooks/setup_services.yml"
    end
  end

end