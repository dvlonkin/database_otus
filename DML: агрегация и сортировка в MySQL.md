1. Группировки с использованием CASE, HAVING, ROLLUP, GROUPING()
```
SELECT
    c.name AS category_name,
    CASE
        WHEN GROUPING(p.product_id) = 1 THEN 'Total'
        ELSE p.name
    END AS product_name,
    MIN(pr.price) AS min_price,
    MAX(pr.price) AS max_price,
    COUNT(pr.price) AS num_offers
FROM
    Products p
JOIN
    Prices pr ON p.product_id = pr.product_id
JOIN
    Categories c ON p.category_id = c.category_id
GROUP BY
    c.name, p.product_id WITH ROLLUP
HAVING
    GROUPING(p.product_id) = 1 OR MIN(pr.price) > 0;
```
2. Добавление максимальной и минимальной цены и количества предложений для каждого продукта
```
SELECT
    p.product_id,
    p.name AS product_name,
    MIN(pr.price) AS min_price,
    MAX(pr.price) AS max_price,
    COUNT(pr.price) AS num_offers
FROM
    Products p
JOIN
    Prices pr ON p.product_id = pr.product_id
GROUP BY
    p.product_id;
```
3. Выборка, показывающая самый дорогой и самый дешевый товар в каждой категории
```
SELECT
    c.name AS category_name,
    MAX(pr.price) AS max_price,
    MIN(pr.price) AS min_price
FROM
    Products p
JOIN
    Prices pr ON p.product_id = pr.product_id
JOIN
    Categories c ON p.category_id = c.category_id
GROUP BY
    c.name;
```
4. ROLLUP с количеством товаров по категориям
```
SELECT
    c.name AS category_name,
    COUNT(p.product_id) AS num_products
FROM
    Products p
JOIN
    Categories c ON p.category_id = c.category_id
GROUP BY
    c.name WITH ROLLUP;
```
