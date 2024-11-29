### MySQL

```
root@mysql001-otus:/install# systemctl status mysql.service
● mysql.service - MySQL Community Server
     Loaded: loaded (/usr/lib/systemd/system/mysql.service; enabled; preset: enabled)
     Active: active (running) since Sat 2024-11-30 00:47:46 MSK; 21s ago
       Docs: man:mysqld(8)
             http://dev.mysql.com/doc/refman/en/using-systemd.html
   Main PID: 5231 (mysqld)
     Status: "Server is operational"
      Tasks: 35 (limit: 4615)
     Memory: 428.7M (peak: 442.7M)
        CPU: 778ms
     CGroup: /system.slice/mysql.service
             └─5231 /usr/sbin/mysqld

root@mysql001-otus:/etc/mysql# free -h
               total        used        free      shared  buff/cache   available
Mem:           3.8Gi       2.0Gi       186Mi        32Mi       2.0Gi       1.9Gi
Swap:          3.8Gi       524Ki       3.8Gi


mysql> SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
+-------------------------+------------+
| Variable_name           | Value      |
+-------------------------+------------+
| innodb_buffer_pool_size | 3221225472 |
+-------------------------+------------+
1 row in set (0.00 sec)
mysql> SHOW VARIABLES LIKE 'datadir';
+---------------+------------------+
| Variable_name | Value            |
+---------------+------------------+
| datadir       | /database/mysql/ |
+---------------+------------------+
1 row in set (0.00 sec)


mysql> CREATE DATABASE intenet_shop;
Query OK, 1 row affected (0.00 sec)

mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| intenet_shop       |
| mysql              |
| performance_schema |
| sys                |
+--------------------+

mysql> CREATE DATABASE intenet_shop;
Query OK, 1 row affected (0.00 sec)

root@mysql001-otus:/etc/mysql# sysbench /usr/share/sysbench/oltp_read_write.lua --mysql-db=testdb --mysql-user=root --mysql-password=123123 --db-driver=mysql --tables=1 --table-size=1000000 --threads=8 --time=60 run
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 8
Initializing random number generator from current time


Initializing worker threads...

Threads started!

SQL statistics:
    queries performed:
        read:                            350952
        write:                           100272
        other:                           50136
        total:                           501360
    transactions:                        25068  (417.70 per sec.)
    queries:                             501360 (8353.96 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          60.0140s
    total number of events:              25068

Latency (ms):
         min:                                    5.36
         avg:                                   19.15
         max:                                 3732.79
         95th percentile:                       21.89
         sum:                               480013.24

Threads fairness:
    events (avg/stddev):           3133.5000/14.65
    execution time (avg/stddev):   60.0017/0.00


```
