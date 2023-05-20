useradd vagrant
 echo 'vagrant:vagrant' | sudo chpasswd
usermod  -a  -G sudo   vagrant  
sudo mkdir /home/vagrant
sudo usermod --shell /bin/bash --home /home/vagrant      vagrant
sudo chown -R vagrant:vagrant    /home/vagrant