# install maxscale


maxctrl create user devops grz7dNKKU73b57Gsh47z --type=admin

- [server1], [server2] là thông tin các node RDS Instance của bạn bao gồm IP Address, Port tương ứng.

- [read-write-service] Read Write Service cho phép bạn phân tách các query read chỉ vào các node Slave và query Write chỉ vào node Master. Bạn thay đổi các thông tin servers, user/password để thực hiện query cho phù hợp.

- [read-write-listerner] cho phép bạn cấu hình port của MaxScale để Application connect đến. Lưu ý: bạn cần mở thêm Security Group Rule chiều Inbound cho port đã chọn để Application của bạn có thể connect được tới MaxScale.

# Tạo user maxscale
CREATE USER 'mykiot_user'@'%' IDENTIFIED BY 'mykiot_password';
GRANT SELECT ON mysql.user TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.db TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.tables_priv TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.columns_priv TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.procs_priv TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.proxies_priv TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.roles_mapping TO 'mykiot_user'@'%';
GRANT SHOW DATABASES ON *.* TO 'mykiot_user'@'%';

User này để chính Maxscale thực hiện kết nối đến DB để query dữ liệu
user này dùng trong [Read-Write-Service] và [MariaDB-Monitor]

# tạo user monitor for maxscale (monitor trạng thái DB)
CREATE USER 'monitor_user'@'%' IDENTIFIED BY 'mykiot_password';
GRANT REPLICATION CLIENT on *.* to 'monitor_user'@'%';

GRANT SUPER, RELOAD on *.* to 'monitor_user'@'%';

show grants for 'monitor_user'@'%' \G;


# 1: config trên UI
# - create Server
Server Name:  mariadb-01
address:  192.168.58.10
monitorpw: mykiot_password
monitoruser: monitor_user
protocol: MariaDBBackend

# - create Mariadb-monitor
Monitor Name: Mairadb-monitor
Module: mariadbmon
auto_failover: true  (chuyển đổi dự phòng tự động)
auto_rejoin: true   (tự động join lại)
replication_user: user tạo replicas
replication_password: 
user: mykiot_user    (user maxscale)
password: mykiot_password   (password maxscale)

- các option thêm
backup_storage_address: xxx  (server backup data)
backup_storage_path: xxx (đường dẫn backup)  (2 cái backup t chưa set)
enforce_read_only_slaves: true/false  (chỉ đọc trên tất cả slave DB)
enforce_writable_master:  true/false  (disble chỉ đọc trên master server hiện tại)

# - create Read-Write-Service
- [read-write-service] Read Write Service cho phép bạn phân tách các query read chỉ vào các node Slave và query Write chỉ vào node Master. Bạn thay đổi các thông tin servers, user/password để thực hiện query cho phù hợp.

Service
Server Name: Read-Write-Service
router: readwritesplit
causal_reads: global
master_accept_reads: true  (master chấp nhận đọc)
master_failure_mode: fail_instantly
master_reconnection: true
transaction_replay_attempts: 5
max_connections: 3000
user: mykiot_user    (user maxscale)
password: mykiot_password   (password maxscale)

# - create Listener
- [read-write-listerner] cho phép bạn cấu hình port của MaxScale để Application connect đến. Lưu ý: bạn cần mở thêm Security Group Rule chiều Inbound cho port đã chọn để Application của bạn có thể connect được tới MaxScale.

Listener
Listen Name: Read-Write-Listener
Protocol: MariaDBprotocol
port: 3306   (port maxscale)

---
OKE: giờ check login Mariadb Maxscale bằng cách sử dụng IP maxscale, user/password maxscale
mysql -h 192.168.58.10 -umykiot_user -pmykiot_password
- check switch master DB (Lưu ý master DB ko nhận request)
restart thử DB master: restart DB master mà trên maxscale chuyển slave -> master, master -> slave là oke
vào DB check show db slave sẽ thấy replicas đã được chuyển








CREATE USER 'mykiot_user'@'%' IDENTIFIED BY 'mykiot_password';
GRANT SELECT ON mysql.user TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.db TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.tables_priv TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.columns_priv TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.procs_priv TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.proxies_priv TO 'mykiot_user'@'%';
GRANT SELECT ON mysql.roles_mapping TO 'mykiot_user'@'%';
GRANT SHOW DATABASES ON *.* TO 'mykiot_user'@'%';

CREATE USER 'monitor_user'@'%' IDENTIFIED BY 'mykiot_password';
GRANT REPLICATION CLIENT on *.* to 'monitor_user'@'%';

GRANT SUPER, RELOAD on *.* to 'monitor_user'@'%';

show grants for 'monitor_user'@'%' \G;



CREATE USER 'replication_user'@'%' IDENTIFIED BY 'abc';
GRANT REPLICATION SLAVE ON *.* TO 'replication_user'@'%';
FLUSH PRIVILEGES;
SHOW MASTER STATUS;



docker run -d  --name maxscale    -p 8989:8989  -v $PWD/maxscale.cnf:/etc/maxscale.cnf  mariadb/maxscale

docker run -d  --name maxscale    -p 8989:8989    mariadb/maxscale