Создание базы данных
CREATE DATABASE InternetShop;
USE InternetShop;

Создание табличных пространств
CREATE TABLESPACE ProductsSpace;
CREATE TABLESPACE CategoriesSpace;
CREATE TABLESPACE PricesSpace;
CREATE TABLESPACE SuppliersSpace;
CREATE TABLESPACE ManufacturersSpace;
CREATE TABLESPACE CustomersSpace;
CREATE TABLESPACE PurchasesSpace;

Создание ролей
CREATE ROLE Admin;
CREATE ROLE User;

Создание схемы данных
CREATE SCHEMA ProductsSchema;
CREATE SCHEMA CategoriesSchema;
CREATE SCHEMA PricesSchema;
CREATE SCHEMA SuppliersSchema;
CREATE SCHEMA ManufacturersSchema;
CREATE SCHEMA CustomersSchema;
CREATE SCHEMA PurchasesSchema;

Создание таблиц и распределение их по схемам и табличным пространствам
Таблица Products
CREATE TABLE ProductsSchema.Products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    category_id INT,
    supplier_id INT,
    manufacturer_id INT,
    FOREIGN KEY (category_id) REFERENCES CategoriesSchema.Categories(category_id),
    FOREIGN KEY (supplier_id) REFERENCES SuppliersSchema.Suppliers(supplier_id),
    FOREIGN KEY (manufacturer_id) REFERENCES ManufacturersSchema.Manufacturers(manufacturer_id)
) TABLESPACE ProductsSpace;

Таблица Categories
CREATE TABLE CategoriesSchema.Categories (
    category_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    description TEXT
) TABLESPACE CategoriesSpace;

Таблица Prices
CREATE TABLE PricesSchema.Prices (
    price_id INT PRIMARY KEY AUTO_INCREMENT,
    product_id INT,
    price DECIMAL(10, 2) CHECK (price > 0),
    currency VARCHAR(3),
    date DATE,
    FOREIGN KEY (product_id) REFERENCES ProductsSchema.Products(product_id)
) TABLESPACE PricesSpace;

Таблица Suppliers
CREATE TABLE SuppliersSchema.Suppliers (
    supplier_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    contact_info TEXT
) TABLESPACE SuppliersSpace;

Таблица Manufacturers
CREATE TABLE ManufacturersSchema.Manufacturers (
    manufacturer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    contact_info TEXT
) TABLESPACE ManufacturersSpace;

Таблица Customers
CREATE TABLE CustomersSchema.Customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    contact_info TEXT
) TABLESPACE CustomersSpace;

Таблица Purchases
CREATE TABLE PurchasesSchema.Purchases (
    purchase_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    product_id INT,
    quantity INT CHECK (quantity > 0),
    purchase_date DATE,
    FOREIGN KEY (customer_id) REFERENCES CustomersSchema.Customers(customer_id),
    FOREIGN KEY (product_id) REFERENCES ProductsSchema.Products(product_id)
) TABLESPACE PurchasesSpace;

Создание индексов
CREATE INDEX idx_products_category_id ON ProductsSchema.Products(category_id);
CREATE INDEX idx_products_supplier_id ON ProductsSchema.Products(supplier_id);
CREATE INDEX idx_products_manufacturer_id ON ProductsSchema.Products(manufacturer_id);
CREATE INDEX idx_purchases_customer_id ON PurchasesSchema.Purchases(customer_id);
CREATE INDEX idx_purchases_product_id ON PurchasesSchema.Purchases(product_id);
CREATE INDEX idx_purchases_purchase_date ON PurchasesSchema.Purchases(purchase_date);
CREATE INDEX idx_prices_product_id ON PricesSchema.Prices(product_id);

Создание ограничений
ALTER TABLE ProductsSchema.Products ADD CONSTRAINT unique_product_id UNIQUE (product_id);
ALTER TABLE CategoriesSchema.Categories ADD CONSTRAINT unique_category_id UNIQUE (category_id);
ALTER TABLE PricesSchema.Prices ADD CONSTRAINT unique_price_id UNIQUE (price_id);
ALTER TABLE SuppliersSchema.Suppliers ADD CONSTRAINT unique_supplier_id UNIQUE (supplier_id);
ALTER TABLE ManufacturersSchema.Manufacturers ADD CONSTRAINT unique_manufacturer_id UNIQUE (manufacturer_id);
ALTER TABLE CustomersSchema.Customers ADD CONSTRAINT unique_customer_id UNIQUE (customer_id);
ALTER TABLE PurchasesSchema.Purchases ADD CONSTRAINT unique_purchase_id UNIQUE (purchase_id);

ALTER TABLE PurchasesSchema.Purchases ADD CONSTRAINT chk_quantity CHECK (quantity > 0);
ALTER TABLE PricesSchema.Prices ADD CONSTRAINT chk_price CHECK (price > 0);

Назначение ролей
GRANT ALL PRIVILEGES ON DATABASE InternetShop TO Admin;
GRANT SELECT, INSERT, UPDATE, DELETE ON ProductsSchema.Products TO User;
GRANT SELECT, INSERT, UPDATE, DELETE ON CategoriesSchema.Categories TO User;
GRANT SELECT, INSERT, UPDATE, DELETE ON PricesSchema.Prices TO User;
GRANT SELECT, INSERT, UPDATE, DELETE ON SuppliersSchema.Suppliers TO User;
GRANT SELECT, INSERT, UPDATE, DELETE ON ManufacturersSchema.Manufacturers TO User;
GRANT SELECT, INSERT, UPDATE, DELETE ON CustomersSchema.Customers TO User;
GRANT SELECT, INSERT, UPDATE, DELETE ON PurchasesSchema.Purchases TO User;
