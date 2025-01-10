1. Проверка текущих индексов
```
mysql> SHOW INDEX FROM productsschema.products;
+----------+------------+--------------------------+--------------+-----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table    | Non_unique | Key_name                 | Seq_in_index | Column_name     | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+----------+------------+--------------------------+--------------+-----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| products |          0 | PRIMARY                  |            1 | product_id      | A         |          50 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| products |          1 | supplier_id              |            1 | supplier_id     | A         |          10 |     NULL |   NULL | YES  | BTREE      |         |               | YES     | NULL       |
| products |          1 | manufacturer_id          |            1 | manufacturer_id | A         |          10 |     NULL |   NULL | YES  | BTREE      |         |               | YES     | NULL       |
| products |          1 | idx_products_category_id |            1 | category_id     | A         |          10 |     NULL |   NULL | YES  | BTREE      |         |               | YES     | NULL       |
+----------+------------+--------------------------+--------------+-----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
4 rows in set (0.00 sec)

mysql> SHOW INDEX FROM categoriesschema.categories;
+------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table      | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| categories |          0 | PRIMARY  |            1 | category_id | A         |          10 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
1 row in set (0.01 sec)

mysql> SHOW INDEX FROM priceschema.prices;
+--------+------------+------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table  | Non_unique | Key_name   | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+--------+------------+------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| prices |          0 | PRIMARY    |            1 | price_id    | A         |          50 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| prices |          1 | product_id |            1 | product_id  | A         |          50 |     NULL |   NULL | YES  | BTREE      |         |               | YES     | NULL       |
+--------+------------+------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
2 rows in set (0.00 sec)

mysql> SHOW INDEX FROM suppliersschema.suppliers;
+-----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table     | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+-----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| suppliers |          0 | PRIMARY  |            1 | supplier_id | A         |          10 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+-----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
1 row in set (0.00 sec)

mysql> SHOW INDEX FROM manufacturersschema.manufacturers;
+---------------+------------+----------+--------------+-----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table         | Non_unique | Key_name | Seq_in_index | Column_name     | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+---------------+------------+----------+--------------+-----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| manufacturers |          0 | PRIMARY  |            1 | manufacturer_id | A         |          10 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+---------------+------------+----------+--------------+-----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
1 row in set (0.00 sec)

mysql> SHOW INDEX FROM customersschema.customers;
+-----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table     | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+-----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| customers |          0 | PRIMARY  |            1 | customer_id | A         |          20 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+-----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
1 row in set (0.01 sec)

mysql> SHOW INDEX FROM purchasesschema.purchases;
+-----------+------------+-------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table     | Non_unique | Key_name    | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+-----------+------------+-------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| purchases |          0 | PRIMARY     |            1 | purchase_id | A         |          50 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| purchases |          1 | customer_id |            1 | customer_id | A         |          18 |     NULL |   NULL | YES  | BTREE      |         |               | YES     | NULL       |
| purchases |          1 | product_id  |            1 | product_id  | A         |          50 |     NULL |   NULL | YES  | BTREE      |         |               | YES     | NULL       |
+-----------+------------+-------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
3 rows in set (0.00 sec)
```

2. Добавление или обновление индексов
```
mysql> CREATE INDEX idx_products_supplier_id ON productsschema.products(supplier_id);
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> CREATE INDEX idx_products_manufacturer_id ON productsschema.products(manufacturer_id);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> CREATE INDEX idx_prices_product_id ON priceschema.prices(product_id);
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> CREATE INDEX idx_purchases_customer_id ON purchasesschema.purchases(customer_id);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> CREATE INDEX idx_purchases_product_id ON purchasesschema.purchases(product_id);
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

3. Создание полнотекстового индекса
```
ALTER TABLE productsschema.products
ADD FULLTEXT fulltext_idx (name, description);
```

4. Анализ производительности с использованием EXPLAIN
```
mysql> EXPLAIN SELECT * FROM productsschema.products WHERE name LIKE '%smartphone%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | products | NULL       | ALL  | NULL          | NULL | NULL    | NULL |   50 |    11.11 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM productsschema.products WHERE MATCH(name, description) AGAINST('smartphone');
+----+-------------+----------+------------+----------+---------------+--------------+---------+-------+------+----------+-------------------------------+
| id | select_type | table    | partitions | type     | possible_keys | key          | key_len | ref   | rows | filtered | Extra                         |
+----+-------------+----------+------------+----------+---------------+--------------+---------+-------+------+----------+-------------------------------+
|  1 | SIMPLE      | products | NULL       | fulltext | fulltext_idx  | fulltext_idx | 0       | const |    1 |   100.00 | Using where; Ft_hints: sorted |
+----+-------------+----------+------------+----------+---------------+--------------+---------+-------+------+----------+-------------------------------+
1 row in set, 1 warning (0.00 sec)
```
