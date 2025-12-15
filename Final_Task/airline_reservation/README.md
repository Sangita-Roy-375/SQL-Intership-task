# Airline Reservation System Using SQL (Project -1)

## Introduction:
Project Title: Airline Reservation System
Database Name:  airline_database
The Airline Reservation System project focuses on designing an organized database structure to manage airline operations such as flight schedules, customer information, seat allocation, and booking records. The system is built using SQL on DB Fiddle, where tables are created, constraints are applied, and normalization ensures data accuracy. Queries are implemented for flight search, checking seat availability, and managing bookings. Triggers further automate updates during booking and cancellation. This project highlights the importance of relational database design and showcases how SQL can be used to develop efficient data-driven applications.

## Abstructs:
The Airline Reservation System is a SQL-based project designed to manage flights, customers, and bookings efficiently. It uses DB Fiddle to create a normalized database with constraints, perform CRUD operations, check seat availability, and run flight search queries. Triggers handle booking updates and cancellations automatically. This project demonstrates practical skills in SQL design and data management.

## Tools Used:
DB Fiddle

## Steps Involved in Building the Project:
### 1. Create a database:
CREATE DATABASE airline_database;

### 2. Create Tables :
CREATE TABLE flights (
    flight_id INT AUTO_INCREMENT PRIMARY KEY,
    flight_number VARCHAR(20) NOT NULL UNIQUE,
    source VARCHAR(100) NOT NULL,
    destination VARCHAR(100) NOT NULL,
    departure_time DATETIME NOT NULL,
    arrival_time DATETIME NOT NULL,
    total_seats INT NOT NULL CHECK (total_seats > 0)
);


CREATE TABLE customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    full_name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    phone VARCHAR(15)
);

CREATE TABLE bookings (
    booking_id INT AUTO_INCREMENT PRIMARY KEY,
    flight_id INT NOT NULL,
    customer_id INT NOT NULL,
    booking_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    seat_no INT NOT NULL,
    status ENUM('CONFIRMED','CANCELLED') DEFAULT 'CONFIRMED',
    FOREIGN KEY (flight_id) REFERENCES flights(flight_id),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    UNIQUE (flight_id, seat_no)   
);

CREATE TABLE seats (
    seat_id INT AUTO_INCREMENT PRIMARY KEY,
    flight_id INT NOT NULL,
    seat_no VARCHAR(5) NOT NULL,
    is_available ENUM('YES', 'NO') DEFAULT 'YES',
    FOREIGN KEY (flight_id) REFERENCES flights(flight_id),
    UNIQUE (flight_id, seat_no)
);

### 3. Insert data into tables:
INSERT INTO flights (flight_number, source, destination, departure_time, arrival_time, total_seats)
VALUES
('AI101', 'Kolkata', 'Delhi', '2025-12-05 10:00:00', '2025-12-05 12:30:00', 150),
('AI202', 'Delhi', 'Mumbai', '2025-12-06 15:00:00', '2025-12-06 17:15:00', 180),
('AI303', 'Mumbai', 'Chennai', '2025-12-07 06:30:00', '2025-12-07 08:45:00', 160),
('AI404', 'Bangalore', 'Hyderabad', '2025-12-08 11:00:00', '2025-12-08 12:10:00', 120),
('AI505', 'Delhi', 'Kolkata', '2025-12-09 14:20:00', '2025-12-09 16:50:00', 180),
('AI606', 'Chennai', 'Goa', '2025-12-10 09:00:00', '2025-12-10 11:00:00', 150),
('AI707', 'Kolkata', 'Bangalore', '2025-12-11 17:15:00', '2025-12-11 19:45:00', 200);


INSERT INTO customers (full_name, email, phone)
VALUES
('Sangita Roy', 'sangita@example.com', '9876543210'),
('Soumitra Das', 'soumitra@example.com', '9123456780'),
('Amit Kumar', 'amitk@example.com', '9001112233'),
('Priya Sharma', 'priya12@example.com', '9011223344'),
('Rahul Gupta', 'rahul.g@example.com', '9022334455'),
('Sneha Bose', 'snehab@example.com', '9033445566'),
('Arjun Singh', 'arjuns@example.com', '9044556677');


INSERT INTO bookings (flight_id, customer_id, seat_no)
VALUES
(1, 1, 12),
(1, 2, 14),
(2, 1, 22),
(3, 3, 10),
(3, 4, 15),
(4, 2, 7),
(4, 5, 12),
(5, 1, 22),
(6, 4, 18),
(7, 2, 25);

INSERT INTO seats (flight_id, seat_no, is_available) VALUES
(1, 'A1', 'YES'),
(1, 'A2', 'YES'),
(1, 'A3', 'NO'),
(2, 'A4', 'YES'),
(2, 'A5', 'NO'),
(4, 'A6', 'YES'),
(3, 'B1', 'YES'),
(7, 'B2', 'NO'),
(4, 'B3', 'YES'),
(3, 'B4', 'YES'),
(5, 'B5', 'YES'),
(6, 'B6', 'NO');

### 4. View all records:
select * from flights;
select * from customers;
select * from bookings;

### 5. Find Available Seats for a Flight:
SELECT 
    f.flight_id,
    f.flight_number,
    f.total_seats - COUNT(b.seat_no) AS available_seats
FROM flights f
LEFT JOIN bookings b ON f.flight_id = b.flight_id AND b.status = 'CONFIRMED'
WHERE f.flight_id = 1
GROUP BY f.flight_id;

### 6. Flight Search (By Source & Destination & Date):
SELECT *
FROM flights
WHERE source = 'Kolkata'
  AND destination = 'Delhi'
  AND DATE(departure_time) = '2025-12-05';

### 7. List All Confirmed Bookings:
SELECT 
    b.booking_id,
    c.full_name,
    f.flight_number,
    b.seat_no,
    b.status
FROM bookings b
JOIN customers c ON b.customer_id = c.customer_id
JOIN flights f ON b.flight_id = f.flight_id
WHERE b.status = 'CONFIRMED';

### 8. Booking Summary Report:
SELECT 
    f.flight_number,
    f.source,
    f.destination,
    COUNT(b.booking_id) AS total_bookings,
    SUM(CASE WHEN b.status='CONFIRMED' THEN 1 ELSE 0 END) AS confirmed,
    SUM(CASE WHEN b.status='CANCELLED' THEN 1 ELSE 0 END) AS cancelled
FROM flights f
LEFT JOIN bookings b ON f.flight_id = b.flight_id
GROUP BY f.flight_number;

## Conclusion:
The Airline Reservation System project successfully demonstrates the use of SQL to design and implement a structured database for managing flight information, customer details, seat allocation, and booking records. By applying normalization and appropriate constraints, the system ensures data accuracy and consistency. SQL queries enable efficient flight searches and seat availability checks, while triggers automate booking updates and prevent overbooking or handle cancellations effectively. Overall, this project highlights the practical application of relational database concepts and showcases the importance of SQL in building reliable and efficient data-driven airline reservation systems.
