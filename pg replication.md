### physical replication
```
root@pg0-otus:~# pg_lsclusters
Ver Cluster Port Status Owner    Data directory Log file
15  main    5432 online postgres /pg_data/data  /var/log/postgresql/postgresql-15-main.log

2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:31:b7:90 brd ff:ff:ff:ff:ff:ff
    inet 192.168.88.65/24 brd 192.168.88.255 scope global dynamic noprefixroute enp0s3

postgres=# \l
                                                   List of databases
     Name     |  Owner   | Encoding |   Collate   |    Ctype    | ICU Locale | Locale Provider |   Access privileges
--------------+----------+----------+-------------+-------------+------------+-----------------+-----------------------
 DML          | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |
 internetshop | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =Tc/postgres         +
              |          |          |             |             |            |                 | postgres=CTc/postgres+
              |          |          |             |             |            |                 | admin=CTc/postgres

```
```
root@pg1-otus:~# pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log

2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:34:4b:05 brd ff:ff:ff:ff:ff:ff
    inet 192.168.88.64/24 brd 192.168.88.255 scope global dynamic noprefixroute enp0s3

```

- модифицируем pg_hba.conf на pg_0
```
host    replication     all             192.168.88.64/32        trust
```
- на pg_1 добавим задержку 5минут

```
recovery_min_apply_delay = '5min'
```
- запускаем посстановление на реплике

```
pg_basebackup -h 192.168.88.65 -R -D /var/lib/postgresql/15/main -U postgres

root@pg1-otus:~# pg_ctlcluster 15 main start
root@pg1-otus:~# pg_lsclusters
Ver Cluster Port Status          Owner    Data directory              Log file
15  main    5432 online,recovery postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```
- на pg0 видим сессию реплики
```
postgres=# select * from pg_stat_replication \gx
-[ RECORD 1 ]----+------------------------------
pid              | 3934
usesysid         | 10
usename          | postgres
application_name | 15/main
client_addr      | 192.168.88.64
client_hostname  |
client_port      | 57758
backend_start    | 2024-11-29 22:04:23.105503+03
backend_xmin     |
state            | streaming
sent_lsn         | 0/5000148
write_lsn        | 0/5000148
flush_lsn        | 0/5000148
replay_lsn       | 0/5000148
write_lag        |
flush_lag        |
replay_lag       |
sync_priority    | 0
sync_state       | async
reply_time       | 2024-11-29 22:14:33.397866+03
```
- на pg1
```
postgres=# select * from pg_stat_wal_receiver \gx
-[ RECORD 1 ]---------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
pid                   | 25877
status                | streaming
receive_start_lsn     | 0/5000000
receive_start_tli     | 1
written_lsn           | 0/5000780
flushed_lsn           | 0/5000780
received_tli          | 1
last_msg_send_time    | 2024-11-29 22:51:11.300021+03
last_msg_receipt_time | 2024-11-29 22:51:11.299594+03
latest_end_lsn        | 0/5000780
latest_end_time       | 2024-11-29 22:40:05.851694+03
slot_name             |
sender_host           | 192.168.88.65
sender_port           | 5432
conninfo              | user=postgres passfile=/var/lib/postgresql/.pgpass channel_binding=prefer dbname=replication host=192.168.88.65 port=5432 fallback_application_name=15/main sslmode=prefer sslnegotiation=postgres sslcompression=0 sslcertmode=allow sslsni=1 ssl_min_protocol_version=TLSv1.2 gssencmode=prefer krbsrvname=postgres gssdelegation=0 target_session_attrs=any load_balance_hosts=disable
```
- добавляем запись в таблицу на pg0
```
DML=# INSERT INTO statistic values ('test1',1,2024,10);
INSERT 0 1
DML=# select now();
              now
-------------------------------
 2024-11-29 22:37:46.720527+03
(1 row)

DML=# select * from statistic ;
 player_name | player_id | year_game | points
-------------+-----------+-----------+--------
 Mike        |         1 |      2018 |  18.00
 Jack        |         2 |      2018 |  14.00
 Jackie      |         3 |      2018 |  30.00
 Jet         |         4 |      2018 |  30.00
 Luke        |         1 |      2019 |  16.00
 Mike        |         2 |      2019 |  14.00
 Jack        |         3 |      2019 |  15.00
 Jackie      |         4 |      2019 |  28.00
 Jet         |         5 |      2019 |  25.00
 Luke        |         1 |      2020 |  19.00
 Mike        |         2 |      2020 |  17.00
 Jack        |         3 |      2020 |  18.00
 Jackie      |         4 |      2020 |  29.00
 Jet         |         5 |      2020 |  27.00
 test1       |         1 |      2024 |  10.00
(15 rows)
```
- на pg1 она появляется спустя 5 минут.

```
Every 2.0s: sudo -u postgres psql -c "select * from statistic;" -d DML    pg1-otus: Fri Nov 29 22:41:00 2024

 player_name | player_id | year_game | points
-------------+-----------+-----------+--------
 Mike        |         1 |      2018 |  18.00
 Jack        |         2 |      2018 |  14.00
 Jackie      |         3 |      2018 |  30.00
 Jet         |         4 |      2018 |  30.00
 Luke        |         1 |      2019 |  16.00
 Mike        |         2 |      2019 |  14.00
 Jack        |         3 |      2019 |  15.00
 Jackie      |         4 |      2019 |  28.00
 Jet         |         5 |      2019 |  25.00
 Luke        |         1 |      2020 |  19.00
 Mike        |         2 |      2020 |  17.00
 Jack        |         3 |      2020 |  18.00
 Jackie      |         4 |      2020 |  29.00
 Jet         |         5 |      2020 |  27.00
(14 rows)


Every 2.0s: sudo -u postgres psql -c "select * from statistic;" -d DML    pg1-otus: Fri Nov 29 22:42:30 2024

 player_name | player_id | year_game | points
-------------+-----------+-----------+--------
 Mike        |         1 |      2018 |  18.00
 Jack        |         2 |      2018 |  14.00
 Jackie      |         3 |      2018 |  30.00
 Jet         |         4 |      2018 |  30.00
 Luke        |         1 |      2019 |  16.00
 Mike        |         2 |      2019 |  14.00
 Jack        |         3 |      2019 |  15.00
 Jackie      |         4 |      2019 |  28.00
 Jet         |         5 |      2019 |  25.00
 Luke        |         1 |      2020 |  19.00
 Mike        |         2 |      2020 |  17.00
 Jack        |         3 |      2020 |  18.00
 Jackie      |         4 |      2020 |  29.00
 Jet         |         5 |      2020 |  27.00
 test1       |         1 |      2024 |  10.00
(15 rows)
```
### logical replication
-на pg0 устанавливаем wal_level = logical
```
postgres=# show wal_level ;
 wal_level
-----------
 logical
(1 row)
```
- создадим публикацию
```
DML=# create publication test_pub for table statistic ;
CREATE PUBLICATION
DML=# \dRp+
                            Publication test_pub
  Owner   | All tables | Inserts | Updates | Deletes | Truncates | Via root
----------+------------+---------+---------+---------+-----------+----------
 postgres | f          | t       | t       | t       | t         | f
Tables:
    "public.statistic"
```
-на pg1 создаем подниску
```postgres=# \c DML
You are now connected to database "DML" as user "postgres".
DML=# create subscription test_pub connection 'host=192.168.88.65 port=5432 user=postgres password=postgres dbname=DML' PUBLICATION test_pub WITH (copy_data = true);
NOTICE:  created replication slot "test_pub" on publisher
CREATE SUBSCRIPTION
```
```
DML=# \dRs
            List of subscriptions
   Name   |  Owner   | Enabled | Publication
----------+----------+---------+-------------
 test_pub | postgres | t       | {test_pub}
(1 row)
```
- на pg0 добавлем строчку
```
DML=# INSERT INTO statistic values ('test2',2,2024,20);
INSERT 0 1
DML=# select *  from statistic ;
 player_name | player_id | year_game | points
-------------+-----------+-----------+--------
 Mike        |         1 |      2018 |  18.00
 Jack        |         2 |      2018 |  14.00
 Jackie      |         3 |      2018 |  30.00
 Jet         |         4 |      2018 |  30.00
 Luke        |         1 |      2019 |  16.00
 Mike        |         2 |      2019 |  14.00
 Jack        |         3 |      2019 |  15.00
 Jackie      |         4 |      2019 |  28.00
 Jet         |         5 |      2019 |  25.00
 Luke        |         1 |      2020 |  19.00
 Mike        |         2 |      2020 |  17.00
 Jack        |         3 |      2020 |  18.00
 Jackie      |         4 |      2020 |  29.00
 Jet         |         5 |      2020 |  27.00
 test1       |         1 |      2024 |  10.00
 test2       |         2 |      2024 |  20.00
(16 rows)
```
- pg1 очщаем таблицу и данные синхронизируются.
```
DML=# truncate statistic ;
TRUNCATE TABLE
```
```
DML=# select * from statistic ;
 player_name | player_id | year_game | points
-------------+-----------+-----------+--------
 Mike        |         1 |      2018 |  18.00
 Jack        |         2 |      2018 |  14.00
 Jackie      |         3 |      2018 |  30.00
 Jet         |         4 |      2018 |  30.00
 Luke        |         1 |      2019 |  16.00
 Mike        |         2 |      2019 |  14.00
 Jack        |         3 |      2019 |  15.00
 Jackie      |         4 |      2019 |  28.00
 Jet         |         5 |      2019 |  25.00
 Luke        |         1 |      2020 |  19.00
 Mike        |         2 |      2020 |  17.00
 Jack        |         3 |      2020 |  18.00
 Jackie      |         4 |      2020 |  29.00
 Jet         |         5 |      2020 |  27.00
 test1       |         1 |      2024 |  10.00
 test2       |         2 |      2024 |  20.00
(16 rows)
```
