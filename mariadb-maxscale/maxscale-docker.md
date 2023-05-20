# install maxscale docker, thiết lập UI
docker run -d  --name maxscale    -p 8989:8989    mariadb/maxscale


# install maxscale docker dùng file config 

docker run -d  --name maxscale    -p 8989:8989  -v $PWD/maxscale.cnf:/etc/maxscale.cnf  mariadb/maxscale


maxctrl create user devops grz7dNKKU73b57Gsh47z --type=admin

nano $PWD/maxscale.cnf

# MaxScale documentation:
# https://mariadb.com/kb/en/mariadb-maxscale-6/

# Global parameters
#
# Complete list of configuration options:
# https://mariadb.com/kb/en/mariadb-maxscale-6-mariadb-maxscale-configuration-guide/

[maxscale]
admin_secure_gui=false
threads=1
admin_host=0.0.0.0
skip_permission_checks=true
log_augmentation=1
ms_timestamp=1
syslog=1
admin_enabled=1
admin_port=8989

# Server definitions
#
# Set the address of the server to the network
# address of a MariaDB server.
#

# [server1]
[Mariadb-01]
address=192.168.58.10
monitorpw=mykiot_password
monitoruser=monitor_user
protocol=MariaDBBackend
type=server

# [server2]
[Mariadb-02]
address=192.168.58.11
monitorpw=mykiot_password
monitoruser=monitor_user
protocol=MariaDBBackend
type=server
# user monitor maxscale


[Mairadb-monitor]
auto_failover=true
auto_rejoin=true
replication_password=vietdung123
replication_user=vietdung
module=mariadbmon
password=mykiot_password
servers=Mariadb-01,Mariadb-02
type=monitor
user=mykiot_user 
# (user maxscale, ko phải user monitor)

# user này tạo trên DB để có quyền trên maxscale UI

# Service definitions
#
# Service Definition for a read-only service and
# a read/write splitting service.
#
# Create the service user with:
#
#  CREATE USER 'service_user'@'%' IDENTIFIED BY 'service_pw';
#  GRANT SELECT ON mysql.user TO 'service_user'@'%';
#  GRANT SELECT ON mysql.db TO 'service_user'@'%';
#  GRANT SELECT ON mysql.tables_priv TO 'service_user'@'%';
#  GRANT SELECT ON mysql.columns_priv TO 'service_user'@'%';
#  GRANT SELECT ON mysql.procs_priv TO 'service_user'@'%';
#  GRANT SELECT ON mysql.proxies_priv TO 'service_user'@'%';
#  GRANT SELECT ON mysql.roles_mapping TO 'service_user'@'%';
#  GRANT SHOW DATABASES ON *.* TO 'service_user'@'%';

[Read-Write-Service]
causal_reads=global
master_accept_reads=true
max_connections=3000
password=mykiot_password
router=readwritesplit
type=service
user=mykiot_user
servers=Mariadb-01,Mariadb-02
causal_reads_timeout=5s
master_failure_mode=fail_instantly

[Read-Write-Listener]
port=3306
service=Read-Write-Service
type=listener



- [server1], [server2] là thông tin các node RDS Instance của bạn bao gồm IP Address, Port tương ứng.

- [read-write-service] Read Write Service cho phép bạn phân tách các query read chỉ vào các node Slave và query Write chỉ vào node Master. Bạn thay đổi các thông tin servers, user/password để thực hiện query cho phù hợp.

- [read-write-listerner] cho phép bạn cấu hình port của MaxScale để Application connect đến. Lưu ý: bạn cần mở thêm Security Group Rule chiều Inbound cho port đã chọn để Application của bạn có thể connect được tới MaxScale.