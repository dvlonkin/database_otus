```
root@pg0-otus:~# systemctl status postgresql  
● postgresql.service - PostgreSQL RDBMS  
     Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; preset: enabled)  
     Active: active (exited) since Sat 2024-09-21 19:36:58 MSK; 4min 58s ago  
    Process: 6433 ExecStart=/bin/true (code=exited, status=0/SUCCESS)  
   Main PID: 6433 (code=exited, status=0/SUCCESS)  
        CPU: 1ms  

Sep 21 19:36:58 pg0-otus systemd[1]: Starting postgresql.service - PostgreSQL RDBMS...
Sep 21 19:36:58 pg0-otus systemd[1]: Finished postgresql.service - PostgreSQL RDBMS.
```
```
root@pg0-otus:~# ss -tunlp  
Netid          State           Recv-Q          Send-Q                   Local Address:Port                    Peer Address:Port         Process  
tcp            LISTEN          0               244                      192.168.88.65:5432                         0.0.0.0:*             users:(("postgres",pid=6414,fd=5))
```
```
          server_version
----------------------------------
 15.8 (Ubuntu 15.8-1.pgdg24.04+1)
```
```
                                          List of roles
 Role name |                         Attributes                         | Member of | Description
-----------+------------------------------------------------------------+-----------+-------------
 admin     |                                                            | {}        |
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}        |
```
```
postgres=# \l
                                                 List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    | ICU Locale | Locale Provider |   Access privileges
-----------+----------+----------+-------------+-------------+------------+-----------------+-----------------------
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |
 store     | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +
           |          |          |             |             |            |                 | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +
           |          |          |             |             |            |                 | postgres=CTc/postgres
(4 rows)
```
![](https://github.com/dvlonkin/database_otus/blob/dd9587d257ac765b337897ee4a54f3d980e7c655/pg0-1.png)
![](https://github.com/dvlonkin/database_otus/blob/bb8b94670b47a244270685d408a8534d61d52ed5/pg0-2.png)
