node_count = Integer(ENV['QDB_NODE_COUNT'] || 2)
vm_memory = ENV['QDB_VM_MEMORY'] || 1024
vm_cpus = Integer(ENV['QDB_VM_CPUS'] || 1)
license_key = ENV['QDB_LICENSE_KEY'] || " "
replication_factor = Integer(ENV['QDB_REPLICATION_FACTOR'] || 1)
log_level = ENV['QDB_LOG_LEVEL'] || "info"

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = vm_memory
    vb.cpus = vm_cpus
  end

  config.vm.define "testbox", primary: true do |testbox|
    testbox.vm.network "forwarded_port", guest: 8080, host: 8080
    testbox.vm.network "private_network", ip: "10.0.0.4"

    testbox.vm.provision "file", source: "testbox/", destination: "/tmp/"

    testbox.vm.provision "shell", args: [log_level], inline: <<-SHELL
      apt install -y ntp
      tar xvzf /tmp/testbox/*.tar.gz -C /usr/
      rm -f /usr/lib/libqdb_api.so
      dpkg -i /tmp/testbox/*.deb
      rm -rf /tmp/testbox

      export IP_ADDRESS=$(hostname -I | cut -d ' ' -f1)
      echo $(qdb_httpd --gen-config -c "/etc/qdb/qdb_httpd.conf" "--node=10.0.0.10:2836" "--address=$IP_ADDRESS:8080" "--log-level=$1") > /etc/qdb/qdb_httpd.conf
      service qdb_httpd restart
    SHELL
  end

  (1..node_count).each do |i|
    config.vm.define "node-#{i}", primary: true do |node|
      ip_address = "10.0.0.#{i+9}"
      node.vm.network "private_network", ip: ip_address

      node.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
        vb.cpus = 1
      end

      node.vm.provision "file", source: "node/", destination: "/tmp/"

      node.vm.provision "shell", args: ["#{ip_address}:2836",replication_factor,license_key,log_level], inline: <<-SHELL
        apt install -y ntp
        dpkg -i /tmp/node/*.deb
        rm -rf /tmp/node

        echo $(qdbd --gen-config -c "/etc/qdb/qdbd.conf" "--address=$1" "--peer=10.0.0.10:2836" "--replication=$2" "--license-key=$3 " "--log-level=$4") > /etc/qdb/qdbd.conf
        service qdbd restart
      SHELL
    end
  end
end
