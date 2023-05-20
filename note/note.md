2HX6JQEecqihB9q

# web
http://52.77.218.209:8088/
User/ pass: admin / A123456b8899

# add node to pmm server
pmm-admin config --server-insecure-tls --server-url=https://admin:A123456b8899@10.20.1.117:8443 10.20.32.85 generic mongodb-t1-node02 
# 10.20.30.230 generic mongodb-t1-node02  IP and name of host dk add
# install pmm-client
wget https://repo.percona.com/apt/percona-release_latest.generic_all.deb
dpkg -i percona-release_latest.generic_all.deb
apt update -y
apt install -y pmm2-client

# ssh server pmm server
ssh -i "new-mykiot-grafana.pem" ubuntu@ec2-52-77-218-209.ap-southeast-1.compute.amazonaws.com   con prometheus


pmm-admin config --server-insecure-tls --server-url=https://admin:mykiot-admin@10.15.132.31:8443 10.20.32.85 generic mongodb-node02 


# practice
sudo pmm-admin --version
sudo systemctl status node_exporter

sudo wget https://repo.percona.com/apt/percona-release_latest.generic_all.deb
sudo dpkg -i percona-release_latest.generic_all.deb
sudo apt update -y
sudo apt install -y pmm2-client
sudo pmm-admin --version

pmm-admin config --server-insecure-tls --server-url=https://admin:mykiot-admin@10.15.132.31:8443 10.15.133.13  generic mongodb-03

# node_exporter

wget https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz
tar xvfz node_exporter-1.0.1.linux-amd64.tar.gz
sudo mv node_exporter-1.0.1.linux-amd64/node_exporter /usr/local/bin/
sudo useradd -rs /bin/false node_exporter

sudo tee /etc/systemd/system/node_exporter.service<<EOF
[Unit]
Description=Node Exporter
After=network.target
 
[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter
 
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
sudo systemctl status node_exporter


# redis exporter
sudo wget https://github.com/oliver006/redis_exporter/releases/download/v1.45.0/redis_exporter-v1.45.0.linux-amd64.tar.gz
sudo tar xvf redis_exporter-v1.45.0.linux-amd64.tar.gz
sudo mv redis_exporter-v1.45.0.linux-amd64/redis_exporter /usr/local/bin/
sudo chmod +x /usr/local/bin/redis_exporter

sudo groupadd --system prometheus
sudo useradd -s /sbin/nologin --system -g prometheus prometheus


sudo tee /etc/systemd/system/redis_exporter.service<<EOF
[Unit]
Description=Prometheus
Documentation=https://github.com/oliver006/redis_exporter
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=prometheus
Group=prometheus
ExecReload=/bin/kill -HUP $MAINPID
ExecStart=/usr/local/bin/redis_exporter \
  --log-format=txt \
  --namespace=redis \
  --web.listen-address=:9121 \
  --redis.addr="redis://127.0.0.1:6379" \
  --redis.password="TUtoak8wMTg5djI1"
  --web.telemetry-path=/metrics

SyslogIdentifier=redis_exporter
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl restart redis_exporter
sudo systemctl status redis_exporter

# install kafka JMX
cd /usr/share/java/kafka
wget https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.17.1/jmx_prometheus_javaagent-0.17.1.jar

cd /etc/kafka
nano jmx_exporter.yml
https://github.com/oded-dd/prometheus-jmx-kafka/blob/master/kafka-jmx-metric.yaml


nano /usr/bin/kafka-server-start
export KAFKA_OPTS='-javaagent:/usr/share/java/kafka/jmx_prometheus_javaagent-0.17.1.jar=7071:/etc/kafka/jmx_exporter.yml'

nano /etc/systemd/system/confluent-kafka.service
Environment="KAFKA_OPTS=-javaagent:/usr/share/java/kafka/jmx_prometheus_javaagent-0.17.1.jar=7071:/etc/kafka/jmx_exporter.yml"

export KAFKA_OPTS=' -javaagent:/usr/share/java/kafka/jmx_prometheus_javaagent-0.17.1.jar=7071:/etc/kafka/jmx_exporter.yml'



# mysql exporter
sudo groupadd --system prometheus
sudo useradd -s /sbin/nologin --system -g prometheus prometheus

curl -s https://api.github.com/repos/prometheus/mysqld_exporter/releases/latest   | grep browser_download_url   | grep linux-amd64 | cut -d '"' -f 4   | wget -qi -

tar xvf  mysqld_exporter-0.14.0.linux-amd64.tar.gz 
mv mysqld_exporter-0.14.0.linux-amd64/mysqld_exporter /usr/local/bin/
sudo chmod +x /usr/local/bin/mysqld_exporter


CREATE USER '[mysql_user]' IDENTIFIED BY '[mysql_password]'  WITH MAX_USER_CONNECTIONS 3;
GRANT PROCESS, REPLICATION CLIENT, REPLICATION SLAVE, SELECT ON *.* TO '[mysql_user]';
flush privileges;

nano /etc/.mysqld_exporter.cnf
[client]
user=mysqld_exporter
password=mykiot-admin




sudo tee /etc/systemd/system/mysql_exporter.service<<EOF
[Unit]
Description=Prometheus MySQL Exporter
After=network.target
User=prometheus
Group=prometheus

[Service]
Type=simple
Restart=always
ExecStart=/usr/local/bin/mysqld_exporter \
--config.my-cnf /etc/.mysqld_exporter.cnf \
--collect.global_status \
--collect.info_schema.innodb_metrics \
--collect.auto_increment.columns \
--collect.info_schema.processlist \
--collect.binlog_size \
--collect.info_schema.tablestats \
--collect.global_variables \
--collect.info_schema.query_response_time \
--collect.info_schema.userstats \
--collect.info_schema.tables \
--collect.perf_schema.tablelocks \
--collect.perf_schema.file_events \
--collect.perf_schema.eventswaits \
--collect.perf_schema.indexiowaits \
--collect.perf_schema.tableiowaits \
--collect.slave_status \
--web.listen-address=127.0.0.1:9104

[Install]
WantedBy=multi-user.target
EOF



sudo systemctl daemon-reload
sudo systemctl enable mysql_exporter
sudo systemctl start mysql_exporter



# mongodb exporter

wget https://github.com/percona/mongodb_exporter/releases/download/v0.7.1/mongodb_exporter-0.7.1.linux-amd64.tar.gz

tar xvzf mongodb_exporter-0.7.1.linux-amd64.tar.gz
sudo mv mongodb_exporter /usr/local/bin/

  # tao user - luu y day la role cua DB cu the
mongo mongodb://10.15.133.11:27017,10.15.133.12:27017,10.15.133.13:27017

db.createUser({user: "test",pwd: "testing",roles: [{ role: "clusterMonitor", db: "admin" },{ role: "read", db: "local" }]})  

# Next, set your MongoDB URI environment variable with the appropriate authentication credentials:

export MONGODB_URI=mongodb://test:testing@localhost:27017

sudo tee  /lib/systemd/system/mongodb_exporter.service<<EOF


[Unit]
Description=MongoDB Exporter
User=prometheus

[Service]
Type=simple
Restart=always
ExecStart=/usr/local/bin/mongodb_exporter

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl start mongodb_exporter.service

sudo curl http://localhost:9216/metrics

       

# promtail is agent log => deploy on target host

PROMTAIL_VERSION=$(curl -s "https://api.github.com/repos/grafana/loki/releases/latest" | grep -Po '"tag_name": "v\K[0-9.]+')
sudo mkdir /opt/promtail
sudo wget -qO /opt/promtail/promtail.gz "https://github.com/grafana/loki/releases/download/v${PROMTAIL_VERSION}/promtail-linux-amd64.zip"
sudo gunzip /opt/promtail/promtail.gz
sudo chmod a+x /opt/promtail/promtail
sudo ln -s /opt/promtail/promtail /usr/local/bin/promtail
sudo wget -qO /opt/promtail/promtail.yaml "https://raw.githubusercontent.com/grafana/loki/v${PROMTAIL_VERSION}/clients/cmd/promtail/promtail-local-config.yaml"
promtail -version

sudo tee /etc/systemd/system/promtail.service<<EOF
[Unit]
Description=Promtail client for sending logs to Loki
After=network.target

[Service]
ExecStart=/opt/promtail/promtail -config.file=/opt/promtail/promtail.yaml
Restart=always
TimeoutStopSec=3

[Install]
WantedBy=multi-user.target
EOF

sudo tee /opt/promtail/promtail.yaml<<EOF
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://192.168.48.1:3100/loki/api/v1/push

scrape_configs:
- job_name: MongoDB_logs
  static_configs:
  - targets:
      - localhost
    labels:
      job: 	mongodb-1
      __path__: /var/log/mongodb/*.log
EOF

sudo systemctl enable promtail
sudo service promtail restart 
sudo systemctl status promtail
