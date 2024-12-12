1. Вроаналезировав данные проекта, я пришел к выводу, что типы данных БД меняться не будут. [Структура БД.png](https://github.com/dvlonkin/database_otus/blob/8ace7ce100213dec2729f27713f54223ad23a84a/%D0%A1%D1%82%D1%80%D1%83%D0%BA%D1%82%D1%83%D1%80%D0%B0%20%D0%91%D0%94.png)  
2. Вижу смысл добавить josn к таблице Products для хранения отзывов о товаре [Структура БДMySQL.png](https://github.com/dvlonkin/database_otus/blob/8ace7ce100213dec2729f27713f54223ad23a84a/%D0%A1%D1%82%D1%80%D1%83%D0%BA%D1%82%D1%83%D1%80%D0%B0%20%D0%91%D0%94MySQL.drawio.png)  
```
ALTER TABLE Products
ADD COLUMN reviews JSON;
```
- Вставка данных в таблицу Products с полем JSON для отзывов
```
INSERT INTO Products (name, description, category_id, supplier_id, manufacturer_id, reviews) VALUES
('Smartphone', 'A high-end smartphone', 1, 1, 1, '{"reviewer": "John Doe", "rating": 5, "comment": "Great phone!"}'),
('T-Shirt', 'A comfortable cotton t-shirt', 2, 2, 2, '{"reviewer": "Jane Smith", "rating": 4, "comment": "Nice fit!"}'),
('Novel', 'An exciting adventure novel', 3, 3, 3, '{"reviewer": "Alice Johnson", "rating": 5, "comment": "Engaging story!"}');
```
- Получение продуктов с фильтрацией по рейтингу в отзывах:
```
SELECT
    product_id,
    name,
    description,
    category_id,
    supplier_id,
    manufacturer_id,
    reviews
FROM
    Products
WHERE
    JSON_UNQUOTE(JSON_EXTRACT(reviews, '$.rating')) >= 4;
```
