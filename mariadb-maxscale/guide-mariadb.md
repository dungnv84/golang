sudo apt update
sudo apt install mariadb-server -y

# master

nano /etc/mysql/mariadb.conf.d/50-server.cnf

bind-address = 192.168.58.111
server-id=1
#report_host=master
#
log_bin=/var/log/mysql/mariadb-bin
log_bin_index=/var/log/mysql/mariadb-bin.index
#
relay_log=/var/log/mysql/relay-bin
relay_log_index=/var/log/mysql/relay-bin.index

systemctl restart mariadb
---
admyki0t / k0i^5658AagcxDF  
create user vietdung@'%' identified by 'vietdung123';                   
GRANT REPLICATION SLAVE ON *.* TO vietdung@'%' IDENTIFIED BY 'vietdung123'; 
FLUSH PRIVILEGES;
SHOW MASTER STATUS;

create user service_user@'%' identified by 'service_pw';                        
GRANT REPLICATION SLAVE ON *.* TO service_user@'%'  IDENTIFIED BY 'service_pw';
FLUSH PRIVILEGES;
SHOW MASTER STATUS;

# slave

nano /etc/mysql/mariadb.conf.d/50-server.cnf

bind-address = 192.168.58.12
server-id=2
report_host=slave
#
log_bin=/var/log/mysql/mariadb-bin
log_bin_index=/var/log/mysql/mariadb-bin.index
expire-logs-days=10
read_only=1


systemctl restart mariadb
  
mysql
RESET SLAVE ALL;
stop slave;
RESET SLAVE ALL;
change master to
master_host='192.168.58.11',
master_user='replication_user',
master_password='abc',
master_log_file='mariadb-02-bin.000004',
MASTER_USE_GTID=current_pos,
master_log_pos=347;

START SLAVE;
show slave status\G;


# master 

CREATE DATABASE vietdung;
USE vietdung;
CREATE TABLE students (id int, name varchar(20), surname varchar(20));

INSERT INTO students VALUES (1,"hitesh","jethva");
INSERT INTO students VALUES (2,"jayesh","jethva");

INSERT INTO students VALUES (5,"vietdung","jethva");
INSERT INTO students VALUES (6,"vietdung","jethva");
SELECT * FROM students;

# slave

SHOW DATABASES;
USE vietdung;
SHOW TABLES;
SELECT * FROM students;


# reset slave if error
stop slave;
RESET SLAVE ALL;



mysql  -h mykiot-prod-ecatalog-mariadb-rds.cnn1ks4tlxho.ap-southeast-1.rds.amazonaws.com  -u admin -psdjkfhk2342346JFHDJF

mysql  -h mykiot-prod-ecatalog-mariadb-readreplica.cnn1ks4tlxho.ap-southeast-1.rds.amazonaws.com   -u admin -psdjkfhk2342346JFHDJF

 -u admin -psdjkfhk2342346JFHDJF
 -u mykiot -pMHYFf3434fiousRoiwer987434



---
echo >  /etc/mysql/mariadb.conf.d/50-server.cnf

# Ansible managed

# this is read by the standalone daemon and embedded servers
[server]

# this is only for the mysqld standalone daemon
[mysqld]
# Basic settings
user                  = mysql
pid-file              = /run/mysqld/mysqld.pid
socket                = /var/run/mysqld/mysqld.sock
basedir               = /usr
datadir               = /var/lib/mysql
tmpdir                = /tmp
lc-messages-dir       = /usr/share/mysql
lc_messages           = en_US
skip-external-locking
port                  = 3306
bind-address          = 0.0.0.0
default_storage_engine = InnoDB
old_passwords = 0
explicit_defaults_for_timestamp
plugin_load_add = query_response_time
query_response_time_stats = 1
userstat = 1

# Fine tuning
max_connect_errors = 1048576
key_buffer_size = 64M
join_buffer_size = 256K
read_buffer_size = 256K
sort_buffer_size = 256K
table_definition_cache = 8192
table_open_cache = 4096
thread_cache_size = 256
wait_timeout = 1800
connect_timeout = 10
tmp_table_size = 64M
max_heap_table_size = 64M
max_allowed_packet = 64M

# Logging
# warning log-basename is defined later for replication
# some log option may be ignored.
# see: https://mariadb.com/kb/en/mysqld-options/#-log-basename
log_error = /var/log/mysql/error.log
log_queries_not_using_indexes = 0
slow_query_log = 1
slow_query_log_file = /var/log/mysql/mariadb-slow.log
expire_logs_days = 5
log_slow_verbosity = query_plan
long_query_time = 3.0
log_slave_updates = 1

# Query cache
# query_cache_size        = 64M

# Character sets
character-set-server = utf8mb4
collation-server     = utf8mb4_general_ci

# InnoDB
# InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
# Read the manual for more InnoDB related options. There are many!
innodb_stats_on_metadata = 0
innodb_temp_data_file_path = ibtmp1:12M:autoextend:max:64M
innodb_data_file_path= ibdata1:12M:autoextend
innodb_buffer_pool_size = 6G
innodb_buffer_pool_instances = 8
innodb_log_files_in_group = 2
innodb_flush_log_at_trx_commit = 2
innodb_log_file_size = 256M
innodb_log_buffer_size = 8M
innodb_read_io_threads = 4
innodb_write_io_threads = 2
innodb_io_capacity = 300
innodb_io_capacity_max = 2000


# Replication
log_bin = /var/log/mysql/mariadb-bin
server_id = 2
log_basename = dc2p-mykiot-mariadb-01

expire_logs_days = 10
max_binlog_size = 100M
binlog_format = STATEMENT
# the following permits to simplify the process of moving a replica to a master
# role by ensuring that replication is not started on master
skip-slave-start

[mysqldump]
quick
quote-names
max_allowed_packet = 64M

# this is only for embedded server
[embedded]

# This group is only read by MariaDB servers, not by MySQL.
# If you use the same .cnf file for MySQL and MariaDB,
# you can put MariaDB-only options here
[mariadb]

# This group is only read by MariaDB-10.5 servers.
# If you use the same .cnf file for MariaDB of different versions,
# use this group for options that older servers don't understand
[mariadb-10.3] 


