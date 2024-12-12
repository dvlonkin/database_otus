1.
- Добавление нового продукта в таблицу Products.
- Добавление цены для этого продукта в таблицу Prices.
- Запись покупки этого продукта покупателем в таблицу Purchases.
```
DELIMITER //

CREATE PROCEDURE AddProductAndRecordPurchase(
    IN p_name VARCHAR(255),
    IN p_description TEXT,
    IN p_category_id INT,
    IN p_supplier_id INT,
    IN p_manufacturer_id INT,
    IN p_price DECIMAL(10, 2),
    IN p_currency VARCHAR(10),
    IN p_customer_id INT,
    IN p_quantity INT,
    IN p_purchase_date DATE
)
BEGIN
    DECLARE product_id INT;
    DECLARE price_id INT;

    -- Начало транзакции
    START TRANSACTION;

    -- Добавление нового продукта в таблицу Products
    INSERT INTO Products (name, description, category_id, supplier_id, manufacturer_id)
    VALUES (p_name, p_description, p_category_id, p_supplier_id, p_manufacturer_id);

    -- Получение идентификатора нового продукта
    SET product_id = LAST_INSERT_ID();

    -- Добавление цены для нового продукта в таблицу Prices
    INSERT INTO Prices (product_id, price, currency, date)
    VALUES (product_id, p_price, p_currency, CURDATE());

    -- Получение идентификатора новой цены
    SET price_id = LAST_INSERT_ID();

    -- Запись покупки нового продукта покупателем в таблицу Purchases
    INSERT INTO Purchases (customer_id, product_id, quantity, purchase_date)
    VALUES (p_customer_id, product_id, p_quantity, p_purchase_date);

    -- Завершение транзакции
    COMMIT;
END //

DELIMITER ;
```

- Пример использования хранимой процедуры
```
CALL AddProductAndRecordPurchase(
    'New Laptop',
    'A high-performance laptop for gaming and work',
    1,
    1,
    1,
    1299.99,
    'USD',
    1,
    1,
    '2023-10-05'
);
```
2.
```
root@mysql001-otus:~# wget https://cdn.otus.ru/media/public/fb/a2/users-39289-fba2c6.csv
--2024-11-30 19:29:35--  https://cdn.otus.ru/media/public/fb/a2/users-39289-fba2c6.csv
Resolving cdn.otus.ru (cdn.otus.ru)... 188.114.98.233, 188.114.99.233, 2a06:98c1:3123:e000::9, ...
Connecting to cdn.otus.ru (cdn.otus.ru)|188.114.98.233|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 88 [text/csv]
Saving to: ‘users-39289-fba2c6.csv’

users-39289-fba2c6.csv                           100%[==========================================================================================================>]      88  --.-KB/s    in 0s

2024-11-30 19:29:37 (98.7 MB/s) - ‘users-39289-fba2c6.csv’ saved [88/88]

```
```
mysql> LOAD DATA INFILE '/var/lib/mysql-files/users-39289-fba2c6.csv' INTO TABLE users FIELDS TERMINATED BY ',';
Query OK, 3 rows affected (0.01 sec)
Records: 3  Deleted: 0  Skipped: 0  Warnings: 0

mysql> SELECT * FROM users;
+-------------------+--------------------+
| email             | city               |
+-------------------+--------------------+
| admin@example.com | Москва             |
| guest@example.com | NULL               |
| user@example.com  | Волгоград          |
+-------------------+--------------------+
3 rows in set (0.01 sec)
```
```
root@mysql001-otus:~# mysqlimport --local --fields-terminated-by=',' --password=****** --host=localhost testdb /var/lib/mysql-files/users.csv
mysqlimport: [Warning] Using a password on the command line interface can be insecure.
testdb.users: Records: 3  Deleted: 0  Skipped: 0  Warnings: 0

mysql> SELECT * FROM users;
+-------------------+--------------------+
| email             | city               |
+-------------------+--------------------+
| admin@example.com | Москва             |
| guest@example.com | NULL               |
| user@example.com  | Волгоград          |
+-------------------+--------------------+
3 rows in set (0.01 sec)
```
