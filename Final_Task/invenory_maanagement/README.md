# Inventory and Warehouse Management System (Project-2)

## Introduction:
Project Title: Inventory and Warehouse Management System
Database Name: inventory_database
The Inventory and Warehouse Management System is a SQL-based project designed to manage products, warehouses, suppliers, and stock levels efficiently. It uses DB Fiddle to create structured tables, apply constraints, and run queries to monitor inventory and generate low-stock alerts. The project demonstrates effective database design and inventory tracking using SQL.      

## Abstructs:
The Inventory and Warehouse Management System is a SQL-based project developed to track and manage products, warehouses, suppliers, and stock levels efficiently. Using DB Fiddle, the system implements a normalized database structure with constraints to ensure data accuracy. SQL queries are used to monitor inventory levels, triggers generate low-stock alerts, and stored procedures handle stock transfers between warehouses. This project demonstrates practical skills in database design, inventory management, and SQL operations.

## Tools Used:
DB Fiddle

## Steps Involved in Building the Project:
### 1. Create a database:
CREATE DATABASE inventory_database;

### 2. Create Tables :
CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    category VARCHAR(50),
    reorder_level INT NOT NULL CHECK (reorder_level >= 0)
);

CREATE TABLE warehouses (
    warehouse_id INT AUTO_INCREMENT PRIMARY KEY,
    warehouse_name VARCHAR(100) NOT NULL,
    location VARCHAR(100)
);

CREATE TABLE suppliers (
    supplier_id INT AUTO_INCREMENT PRIMARY KEY,
    supplier_name VARCHAR(100) NOT NULL,
    contact VARCHAR(50)
);

CREATE TABLE stock (
    stock_id INT AUTO_INCREMENT PRIMARY KEY,
    product_id INT NOT NULL,
    warehouse_id INT NOT NULL,
    supplier_id INT,
    quantity INT NOT NULL CHECK (quantity >= 0),
    FOREIGN KEY (product_id) REFERENCES products(product_id),
    FOREIGN KEY (warehouse_id) REFERENCES warehouses(warehouse_id),
    FOREIGN KEY (supplier_id) REFERENCES suppliers(supplier_id),
    UNIQUE (product_id, warehouse_id)
);

### 3. Insert data into tables:
INSERT INTO products (product_name, category, reorder_level) VALUES
('Laptop', 'Electronics', 10),
('Keyboard', 'Accessories', 20),
('Mouse', 'Accessories', 30);

INSERT INTO warehouses (warehouse_name, location) VALUES
('Central Warehouse', 'Kolkata'),
('Secondary Warehouse', 'Delhi');

INSERT INTO suppliers (supplier_name, contact) VALUES
('Tech Supplies Ltd', '9876543210'),
('Gadget World', '9123456780');

INSERT INTO stock (product_id, warehouse_id, supplier_id, quantity) VALUES
(1, 1, 1, 25),
(2, 1, 2, 15),
(3, 2, 2, 40);

### 4. Check Stock Levels:
SELECT 
    p.product_name,
    w.warehouse_name,
    s.quantity
FROM stock s
JOIN products p ON s.product_id = p.product_id
JOIN warehouses w ON s.warehouse_id = w.warehouse_id;

### 5. Reorder Alert Query:
SELECT 
    p.product_name,
    s.quantity,
    p.reorder_level
FROM stock s
JOIN products p ON s.product_id = p.product_id
WHERE s.quantity <= p.reorder_level;

### 6. Low-Stock Notification:
CREATE TRIGGER low_stock_alert
AFTER UPDATE ON stock
FOR EACH ROW
BEGIN
    IF NEW.quantity <= (
        SELECT reorder_level 
        FROM products 
        WHERE product_id = NEW.product_id
    ) THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Warning: Stock level is below reorder limit!';
    END IF;
END $$

### 7. Stored Procedure: Transfer Stock:
DELIMITER $$

CREATE PROCEDURE transfer_stock (
    IN p_product_id INT,
    IN from_warehouse INT,
    IN to_warehouse INT,
    IN transfer_qty INT
)
BEGIN
    
    UPDATE stock
    SET quantity = quantity - transfer_qty
    WHERE product_id = p_product_id
      AND warehouse_id = from_warehouse;

    
    INSERT INTO stock (product_id, warehouse_id, quantity)
    VALUES (p_product_id, to_warehouse, transfer_qty)
    ON DUPLICATE KEY UPDATE
    quantity = quantity + transfer_qty;
END $$

DELIMITER ;

## Conclusion:
The Inventory and Warehouse Management System project successfully demonstrates the use of SQL to design a structured and efficient database for managing inventory operations. By implementing normalized tables, constraints, queries, triggers, and stored procedures, the system ensures accurate stock tracking and timely low-stock alerts. This project highlights the importance of relational database design and showcases practical SQL skills in building a reliable and effective inventory management solution.



