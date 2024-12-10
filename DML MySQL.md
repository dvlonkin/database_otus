1. Получение всех продуктов с их категориями, поставщиками и производителями.
```
SELECT
    p.product_id,
    p.name AS product_name,
    p.description,
    c.name AS category_name,
    s.name AS supplier_name,
    m.name AS manufacturer_name
FROM
    Products p
INNER JOIN
    Categories c ON p.category_id = c.category_id
INNER JOIN
    Suppliers s ON p.supplier_id = s.supplier_id
INNER JOIN
    Manufacturers m ON p.manufacturer_id = m.manufacturer_id;
```
2. Получение всех продуктов и их цен, даже если цена не указана.
```
SELECT
    p.product_id,
    p.name AS product_name,
    p.description,
    pr.price,
    pr.currency,
    pr.date
FROM
    Products p
LEFT JOIN
    Prices pr ON p.product_id = pr.product_id;
```
3.
- Получение продуктов с определенной категорией
```
SELECT
    product_id,
    name,
    description
FROM
    Products
WHERE
    category_id = 1;
```
- Получение продуктов с ценой выше определенного значения
```
SELECT
    p.product_id,
    p.name AS product_name,
    pr.price
FROM
    Products p
JOIN
    Prices pr ON p.product_id = pr.product_id
WHERE
    pr.price > 100;
```
- Получение продуктов, добавленных в определенный период
```
SELECT
    product_id,
    name,
    description
FROM
    Products
WHERE
    purchase_date BETWEEN '2023-01-01' AND '2023-12-31';
```
- Получение продуктов, у которых в отзывах есть определенное слово
```
SELECT
    product_id,
    name,
    description,
    reviews
FROM
    Products
WHERE
    JSON_UNQUOTE(JSON_EXTRACT(reviews, '$')) LIKE '%excellent%';
```
- Получение продуктов, у которых количество на складе меньше определенного значения
```
SELECT
    product_id,
    name,
    description,
    quantity
FROM
    Products
WHERE
    quantity < 10;
```
