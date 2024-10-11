- Запрос с регулярным выражением  
```
SELECT *
FROM ProductsSchema.Products
WHERE name REGEXP '^[A-Za-z]+$';
```
Этот запрос выбирает все продукты, у которых название состоит только из букв (без цифр и специальных символов). Регулярное выражение ^[A-Za-z]+$ означает, что строка должна содержать только буквы от начала до конца.+

- Запрос с использованием LEFT JOIN и INNER JOIN
```
SELECT p.name AS product_name, c.name AS category_name, s.name AS supplier_name
FROM ProductsSchema.Products p
LEFT JOIN CategoriesSchema.Categories c ON p.category_id = c.category_id
INNER JOIN SuppliersSchema.Suppliers s ON p.supplier_id = s.supplier_id;
```
Этот запрос выбирает названия продуктов, категорий и поставщиков. Порядок соединений в FROM влияет на результат следующим образом:
LEFT JOIN сначала соединяет таблицу Products с таблицей Categories, включая все записи из Products и соответствующие записи из Categories. Если в Categories нет соответствующей записи, результат будет NULL.
INNER JOIN затем соединяет результат предыдущего соединения с таблицей Suppliers, включая только те записи, которые имеют соответствующие записи в Suppliers.

- Запрос на добавление данных с выводом информации о добавленных строках
```
INSERT INTO ProductsSchema.Products (name, description, category_id, supplier_id, manufacturer_id)
VALUES ('New Product', 'Description of new product', 1, 1, 1)
RETURNING product_id, name, description;
```

Этот запрос добавляет новую запись в таблицу Products и возвращает информацию о добавленной строке (product_id, name, description).

- Запрос с обновлением данных используя UPDATE FROM
```
UPDATE ProductsSchema.Products p
SET p.name = 'Updated Product Name'
FROM SuppliersSchema.Suppliers s
WHERE p.supplier_id = s.supplier_id AND s.name = 'Supplier Name';
```

Этот запрос обновляет название продуктов, которые поставляются определенным поставщиком (Supplier Name). Используется UPDATE FROM для соединения таблиц Products и Suppliers.

- Запрос для удаления данных с оператором DELETE используя join с другой таблицей с помощью USING
```
DELETE FROM ProductsSchema.Products p
USING SuppliersSchema.Suppliers s
WHERE p.supplier_id = s.supplier_id AND s.name = 'Supplier Name';
```

Этот запрос удаляет записи из таблицы Products, которые поставляются определенным поставщиком (Supplier Name). Используется USING для соединения таблиц Products и Suppliers.

- Copy from csv-file

|product_id|name|description|category_id|supplier_id|manufacturer_id|
|----------|----|-----------|-----------|-----------|---------------|
|1|Product A|Description of Product A|1|1|1|
|2|Product B|Description of Product B|2|2|2|
|3|Product C|Description of Product C|3|3|3|
```
COPY ProductsSchema.Products (product_id, name, description, category_id, supplier_id, manufacturer_id)
FROM '/path/to/products.csv'
WITH (FORMAT csv, HEADER);
```
