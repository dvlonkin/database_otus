## Реализация отказоустойчевого кластера PostgreSQL для интернет магазина.

### Для реализации используется:
- 2 инстанса PostgreSQL под управлением Patroni
- 3 инстанса etcd для кворума и хранение конфигурации
- 2 инстанса vip-manager для единой точки подключения пользователей к БД
- 3 инстанса Pgbackrest для архивирования wal-файлов и создание резервных копий
- Superset для создания отчетов

### Принципиальная схема
![image](project_postgresql.jpg)

Состояние нод etcd и patroni
```
root@mysql001-otus:~# etcdctl endpoint health --cluster
http://192.168.88.60:2379 is healthy: successfully committed proposal: took = 1.973148ms
http://192.168.88.65:2379 is healthy: successfully committed proposal: took = 2.599321ms
http://192.168.88.64:2379 is healthy: successfully committed proposal: took = 2.65843ms
root@mysql001-otus:~# etcdctl endpoint status --cluster --write-out=table
+---------------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
|         ENDPOINT          |        ID        | VERSION | DB SIZE | IS LEADER | IS LEARNER | RAFT TERM | RAFT INDEX | RAFT APPLIED INDEX | ERRORS |
+---------------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
| http://192.168.88.64:2379 | 1c938984d2e351c2 |  3.4.30 |   98 kB |     false |      false |        20 |        320 |                320 |        |
| http://192.168.88.60:2379 | 6ea35c095e2a522b |  3.4.30 |   98 kB |      true |      false |        20 |        320 |                320 |        |
| http://192.168.88.65:2379 | 7543f4a133d1e671 |  3.4.30 |  115 kB |     false |      false |        20 |        320 |                320 |        |
+---------------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
```
```
root@pg0-otus:~# patronictl -c /etc/patroni/config.yml list
+ Cluster: otus (7476070226399082672) ---------+----+-----------+--------------+
| Member | Host          | Role    | State     | TL | Lag in MB | Tags         |
+--------+---------------+---------+-----------+----+-----------+--------------+
| pg0    | 192.168.88.65 | Replica | streaming |  5 |         0 | nosync: true |
| pg1    | 192.168.88.64 | Leader  | running   |  5 |           | nosync: true |
+--------+---------------+---------+-----------+----+-----------+--------------+
```

### Структура БД
![image](https://github.com/dvlonkin/database_otus/blob/5c7017cc7672e69dc2e7084c5c492269e878bd3c/%D0%A1%D1%82%D1%80%D1%83%D0%BA%D1%82%D1%83%D1%80%D0%B0%20%D0%91%D0%94.png)
