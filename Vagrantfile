Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/focal64"
    config.winrm.timeout = 1800 # 30 minutes
    config.vm.boot_timeout = 1800 # 30 minutes
    config.vm.provider "virtualbox" do |vb|
        vb.memory = 1048
        vb.cpus = 1
      end
    config.vm.provision "shell", inline: <<-EOF
      apt install net-tools
      ifconfig
      snap install microk8s --classic
      microk8s.status --wait-ready
      microk8s.enable dns
      usermod -a -G microk8s vagrant
      echo "alias kubectl='microk8s.kubectl'" > /home/vagrant/.bash_aliases
      chown vagrant:vagrant /home/vagrant/.bash_aliases
      echo "alias kubectl='microk8s.kubectl'" > /root/.bash_aliases
      chown root:root /root/.bash_aliases
      echo "
    
      #master
      192.168.56.2  microk8s-master-01
      192.168.56.3  microk8s-master-02
      192.168.56.4  microk8s-master-03
  
      #worker
      192.168.56.5  microk8s-worker-01
      192.168.56.6  microk8s-worker-02
      192.168.56.7  microk8s-worker-03
      192.168.56.8  microk8s-worker-04
    
      " > /etc/hosts
      cat /etc/hosts
    EOF
  
    config.vm.define "microk8s_master_01" do |microk8s_master_01|
      microk8s_master_01.vm.hostname = "microk8s-master-01"
      microk8s_master_01.vm.network "public_network", bridge: "VirtualBox Host-Only Ethernet Adapter"
      microk8s_master_01.vm.network "private_network", ip: "192.168.56.2"
      microk8s_master_01.vm.provider "virtualbox" do |vb|
        vb.name = "microk8s-master-01"
      end
    end
    config.vm.define "microk8s_master_02" do |microk8s_master_02|
      microk8s_master_02.vm.hostname = "microk8s-master-02"
      microk8s_master_02.vm.network "public_network", bridge: "VirtualBox Host-Only Ethernet Adapter"
      microk8s_master_02.vm.network "private_network", ip: "192.168.56.3"
      microk8s_master_02.vm.provider "virtualbox" do |vb|
        vb.name = "microk8s-master-02"
      end
    end
    config.vm.define "microk8s_master_03" do |microk8s_master_03|
      microk8s_master_03.vm.hostname = "microk8s-master-03"
      microk8s_master_03.vm.network "public_network", bridge: "VirtualBox Host-Only Ethernet Adapter"
      microk8s_master_03.vm.network "private_network", ip: "192.168.56.4"
      microk8s_master_03.vm.provider "virtualbox" do |vb|
        vb.name = "microk8s-master-03"
      end
    end
    config.vm.define "microk8s_worker_01" do |microk8s_worker_01|
      microk8s_worker_01.vm.hostname = "microk8s-worker-01"
      microk8s_worker_01.vm.network "public_network", bridge: "VirtualBox Host-Only Ethernet Adapter"
      microk8s_worker_01.vm.network "private_network",  ip: "192.168.56.5"
      microk8s_worker_01.vm.provider "virtualbox" do |vb|
        vb.name = "microk8s-woker-01"
      end
    end
    config.vm.define "microk8s_worker_02" do |microk8s_worker_02|
      microk8s_worker_02.vm.hostname = "microk8s-worker-02"
      microk8s_worker_02.vm.network "public_network", bridge: "VirtualBox Host-Only Ethernet Adapter"
      microk8s_worker_02.vm.network "private_network", ip: "192.168.56.6"
      microk8s_worker_02.vm.provider "virtualbox" do |vb|
        vb.name = "microk8s-worker-02"
      end
    end
    config.vm.define "microk8s_worker_03" do |microk8s_worker_03|
      microk8s_worker_03.vm.hostname = "microk8s-worker-03"
      microk8s_worker_03.vm.network "public_network", bridge: "VirtualBox Host-Only Ethernet Adapter"
      microk8s_worker_03.vm.network "private_network", ip: "192.168.56.7"
      microk8s_worker_03.vm.provider "virtualbox" do |vb|
        vb.name = "microk8s-worker-03"
      end
  
    end
    config.vm.define "microk8s_worker_04" do |microk8s_worker_04|
      microk8s_worker_04.vm.hostname = "microk8s-worker-04"
      microk8s_worker_04.vm.network "public_network", bridge: "VirtualBox Host-Only Ethernet Adapter"
      microk8s_worker_04.vm.network "private_network",  ip: "192.168.56.8"
      microk8s_worker_04.vm.provider "virtualbox" do |vb|
        vb.name = "microk8s-worker-04"
      end
    end
  end