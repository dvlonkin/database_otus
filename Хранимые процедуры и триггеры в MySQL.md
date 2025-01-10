1. Создание пользователей
```
CREATE USER 'client'@'localhost' IDENTIFIED BY 'client_password';
CREATE USER 'manager'@'localhost' IDENTIFIED BY 'manager_password';
```
2. Процедура выборки товаров
```
DELIMITER //

CREATE PROCEDURE GetProducts(
    IN p_category_id INT,
    IN p_min_price DECIMAL(10, 2),
    IN p_max_price DECIMAL(10, 2),
    IN p_manufacturer_id INT,
    IN p_sort_field VARCHAR(255),
    IN p_sort_order VARCHAR(4),
    IN p_page INT,
    IN p_page_size INT
)
BEGIN
    DECLARE offset INT;
    SET offset = (p_page - 1) * p_page_size;

    SET @sql = CONCAT(
        'SELECT p.product_id, p.name AS product_name, p.description, c.name AS category_name, s.name AS supplier_name, m.name AS manufacturer_name, pr.price, pr.currency ',
        'FROM productsschema.products p ',
        'JOIN categoriesschema.categories c ON p.category_id = c.category_id ',
        'JOIN suppliersschema.suppliers s ON p.supplier_id = s.supplier_id ',
        'JOIN manufacturersschema.manufacturers m ON p.manufacturer_id = m.manufacturer_id ',
        'JOIN priceschema.prices pr ON p.product_id = pr.product_id ',
        'WHERE (p_category_id IS NULL OR p.category_id = ', p_category_id, ') ',
        'AND (p_min_price IS NULL OR pr.price >= ', p_min_price, ') ',
        'AND (p_max_price IS NULL OR pr.price <= ', p_max_price, ') ',
        'AND (p_manufacturer_id IS NULL OR p.manufacturer_id = ', p_manufacturer_id, ') ',
        'ORDER BY ',
        CASE
            WHEN p_sort_field = 'product_id' THEN 'p.product_id'
            WHEN p_sort_field = 'name' THEN 'p.name'
            WHEN p_sort_field = 'price' THEN 'pr.price'
            ELSE 'p.product_id'
        END,
        ' ', p_sort_order,
        ' LIMIT ', p_page_size, ' OFFSET ', offset
    );

    PREPARE stmt FROM @sql;
    EXECUTE stmt;
    DEALLOCATE PREPARE stmt;
END //

DELIMITER ;
```
3. Применение процедуры
```
mysql> CALL GetProducts(1,100.00,500.00,1,'price','ASC',1,10);
+------------+--------------+--------------------------+---------------+---------------+-------------------+--------+----------+
| product_id | product_name | description              | category_name | supplier_name | manufacturer_name | price  | currency |
+------------+--------------+--------------------------+---------------+---------------+-------------------+--------+----------+
|          7 | Product 7    | Description of Product 7 | Category 1    | Supplier 9    | Manufacturer 1    | 490.85 | USD      |
+------------+--------------+--------------------------+---------------+---------------+-------------------+--------+----------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```
4. Дать права да запуск процедуры пользователю client
```
GRANT EXECUTE ON PROCEDURE internetshop.GetProducts TO 'client'@'localhost';
GRANT SELECT ON productsschema.products TO 'client'@'localhost';
GRANT SELECT ON categoriesschema.categories TO 'client'@'localhost';
GRANT SELECT ON suppliersschema.suppliers TO 'client'@'localhost';
GRANT SELECT ON manufacturersschema.manufacturers TO 'client'@'localhost';
GRANT SELECT ON priceschema.prices TO 'client'@'localhost';

root@mysql001-otus:~# mysql -u client -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 18
Server version: 8.4.3 MySQL Community Server - GPL

Copyright (c) 2000, 2024, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> \u internetshop
Database changed
mysql> CALL GetProducts(1,100.00,500.00,1,'price','ASC',1,10);
+------------+--------------+--------------------------+---------------+---------------+-------------------+--------+----------+
| product_id | product_name | description              | category_name | supplier_name | manufacturer_name | price  | currency |
+------------+--------------+--------------------------+---------------+---------------+-------------------+--------+----------+
|          7 | Product 7    | Description of Product 7 | Category 1    | Supplier 9    | Manufacturer 1    | 490.85 | USD      |
+------------+--------------+--------------------------+---------------+---------------+-------------------+--------+----------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

```
5.Создать процедуру get_orders
```
DELIMITER //

CREATE PROCEDURE get_orders(
    IN p_start_date DATETIME,
    IN p_end_date DATETIME,
    IN p_group_by VARCHAR(50)
)
BEGIN
    DECLARE group_by_clause VARCHAR(255);
    DECLARE select_clause VARCHAR(255);

    -- Определение уровня группировки и соответствующих полей
    CASE p_group_by
        WHEN 'product' THEN
            SET group_by_clause = 'p.product_id, p.name';
            SET select_clause = 'p.product_id, p.name AS product_name';
        WHEN 'category' THEN
            SET group_by_clause = 'c.category_id, c.name';
            SET select_clause = 'c.category_id, c.name AS category_name';
        WHEN 'manufacturer' THEN
            SET group_by_clause = 'm.manufacturer_id, m.name';
            SET select_clause = 'm.manufacturer_id, m.name AS manufacturer_name';
        ELSE
            SET group_by_clause = 'p.product_id, p.name';
            SET select_clause = 'p.product_id, p.name AS product_name';
    END CASE;

    -- Формирование динамического SQL-запроса
    SET @sql = CONCAT(
        'SELECT ', select_clause, ', COUNT(pu.product_id) AS total_sales, SUM(pu.quantity) AS total_quantity ',
        'FROM purchasesschema.purchases pu ',
        'JOIN productsschema.products p ON pu.product_id = p.product_id ',
        'JOIN categoriesschema.categories c ON p.category_id = c.category_id ',
        'JOIN manufacturersschema.manufacturers m ON p.manufacturer_id = m.manufacturer_id ',
        'WHERE pu.purchase_date BETWEEN "', p_start_date, '" AND "', p_end_date, '" ',
        'GROUP BY ', group_by_clause
    );

    -- Выполнение динамического SQL-запроса
    PREPARE stmt FROM @sql;
    EXECUTE stmt;
    DEALLOCATE PREPARE stmt;
END //

DELIMITER ;

6.Права дать пользователю manager на выполнение процедуры
```
GRANT SELECT ON internetshop.* TO 'manager'@'localhost';
GRANT EXECUTE ON PROCEDURE internetshop.get_orders TO 'manager'@'localhost';
mysql -u manager -p
mysql> CALL get_orders('2025-01-01', '2025-01-31', 'category');
+-------------+---------------+-------------+----------------+
| category_id | category_name | total_sales | total_quantity |
+-------------+---------------+-------------+----------------+
|           1 | Category 1    |           1 |              5 |
|           3 | Category 3    |           1 |              3 |
|           7 | Category 7    |           1 |              9 |
|           4 | Category 4    |           1 |             10 |
|           2 | Category 2    |           1 |              2 |
+-------------+---------------+-------------+----------------+
5 rows in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```
