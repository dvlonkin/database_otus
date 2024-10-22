### Индекс на поле category_id
Этот индекс ускоряет поиск продуктов по категории. Он полезен для запросов, которые фильтруют продукты по категории.
```
CREATE INDEX idx_products_category_id ON productsschema.products(category_id);
```
Этот индекс ускоряет поиск продуктов по категории. Он полезен для запросов, которые фильтруют продукты по категории. 
```EXPLAIN ANALYZE
SELECT * FROM productsschema.products
WHERE category_id = 1;
```
```
Bitmap Heap Scan on products  (cost=4.23..10.35 rows=10 width=53) (actual time=0.033..0.038 rows=10 loops=1)
  Recheck Cond: (category_id = 1)
  Heap Blocks: exact=3
  ->  Bitmap Index Scan on idx_products_category_id  (cost=0.00..4.22 rows=10 width=0) (actual time=0.027..0.027 rows=10 loops=1)
        Index Cond: (category_id = 1)
Planning Time: 0.056 ms
Execution Time: 0.051 ms
```
### Индекс для полнотекстового поиска
Этот индекс ускоряет полнотекстовый поиск по полю description. Он полезен для запросов, которые ищут продукты по ключевым словам в описании.
```
CREATE INDEX idx_products_description_fulltext ON productsschema.products USING gin(to_tsvector('english', description));
```
```
EXPLAIN ANALYZE
SELECT * FROM productsschema.products
WHERE to_tsvector('english', description) @@ to_tsquery('english', 'product');
```
```
Bitmap Heap Scan on products  (cost=8.02..13.08 rows=2 width=53) (actual time=0.043..0.076 rows=500 loops=1)
  Recheck Cond: (to_tsvector('english'::regconfig, description) @@ '''product'''::tsquery)
  Heap Blocks: exact=6
  ->  Bitmap Index Scan on idx_products_description_fulltext  (cost=0.00..8.02 rows=2 width=0) (actual time=0.035..0.035 rows=500 loops=1)
        Index Cond: (to_tsvector('english'::regconfig, description) @@ '''product'''::tsquery)
Planning Time: 0.079 ms
Execution Time: 0.102 ms
```
### Индекс на поле name с функцией lower
Этот индекс ускоряет поиск продуктов по имени без учета регистра. Он полезен для запросов, которые ищут продукты по имени в нижнем регистре.
```
CREATE INDEX idx_products_name_lower ON productsschema.products(lower(name));
```
```
EXPLAIN ANALYZE
SELECT * FROM productsschema.products
WHERE lower(name) = 'product a';
```
```
Bitmap Heap Scan on products  (cost=4.29..8.85 rows=2 width=53) (actual time=0.020..0.020 rows=0 loops=1)
  Recheck Cond: (lower((name)::text) = 'product a'::text)
  ->  Bitmap Index Scan on idx_products_name_lower  (cost=0.00..4.29 rows=2 width=0) (actual time=0.018..0.018 rows=0 loops=1)
        Index Cond: (lower((name)::text) = 'product a'::text)
Planning Time: 0.252 ms
Execution Time: 0.032 ms
```
### Индекс на несколько полей
Этот индекс ускоряет поиск продуктов по комбинации категории и поставщика. Он полезен для запросов, которые фильтруют продукты по обеим полям.
```
CREATE INDEX idx_products_category_supplier ON productsschema.products(category_id, supplier_id);
```
```
EXPLAIN ANALYZE
SELECT * FROM productsschema.products
WHERE category_id = 1 AND supplier_id = 1;
```
```
Index Scan using idx_products_category_supplier on products  (cost=0.27..8.29 rows=1 width=53) (actual time=0.016..0.016 rows=0 loops=1)
  Index Cond: ((category_id = 1) AND (supplier_id = 1))
Planning Time: 0.191 ms
Execution Time: 0.025 ms
```
