# BookCafe ERD + Database Design (SQL)

This repository contains the ERD and relational database design for **BookCafe**, a sample bookstore/cafe scenario.  
The design models customers placing orders for books, and suppliers providing the bookstore inventory.

## 📌 Overview
The database captures core business operations:
- Customers place orders
- Orders contain multiple books (with quantity and purchase price)
- Suppliers provide books (a book may have multiple suppliers)

Many-to-many relationships are properly resolved using junction tables:
- `orders_books`
- `suppliers_books`

## 🧱 Entities & Tables

### 1) `customers`
Stores customer contact information.
- **PK:** `customer_id`
- Attributes: `name`, `phone_number`, `email` :contentReference[oaicite:1]{index=1}

### 2) `orders`
Stores purchases made by customers.
- **PK:** `order_id`
- **FK:** `customer_id` → `customers.customer_id`
- Attributes: `date_of_order` :contentReference[oaicite:2]{index=2}

### 3) `books`
Stores books available at BookCafe.
- **PK:** `books_id`
- Attributes: `title`, `author`, `genre`, `price` :contentReference[oaicite:3]{index=3}

### 4) `suppliers`
Stores supplier information.
- **PK:** `suppliers_id`
- Attributes: `supplier_name`, `supplier_contact` :contentReference[oaicite:4]{index=4}

### 5) `orders_books` (junction table)
Resolves the many-to-many relationship between orders and books.
- **FKs:**  
  - `orders_order_id` → `orders.order_id`  
  - `books_books_id` → `books.books_id`
- Attributes: `quantity`, `price` (stores purchased quantity and purchase price at the time of order) :contentReference[oaicite:5]{index=5}

### 6) `suppliers_books` (junction table)
Resolves the many-to-many relationship between suppliers and books.
- **FKs:**  
  - `suppliers_suppliers_id` → `suppliers.suppliers_id`  
  - `books_books_id` → `books.books_id` :contentReference[oaicite:6]{index=6}

## 🔗 Relationships (Cardinality)

- **Customers → Orders (1-to-many)**  
  One customer can place many orders, each order belongs to exactly one customer. :contentReference[oaicite:7]{index=7}

- **Orders ↔ Books (many-to-many via `orders_books`)**  
  An order can contain multiple books, and a book can appear in multiple orders. :contentReference[oaicite:8]{index=8}

- **Suppliers ↔ Books (many-to-many via `suppliers_books`)**  
  A supplier can provide multiple books, and a book may be provided by multiple suppliers. :contentReference[oaicite:9]{index=9}

## ✅ Design Notes
- Junction tables are used to correctly represent many-to-many relationships.
- `orders_books` stores transactional details (`quantity`, `price`) to reflect real-world purchases.
- This structure supports flexible querying and maintains data consistency. :contentReference[oaicite:10]{index=10}

## 🧪 Example Queries (Optional)

```sql
-- 1) List all orders with customer name
SELECT o.order_id, c.name, o.date_of_order
FROM orders o
JOIN customers c ON c.customer_id = o.customer_id;

-- 2) List items in a specific order
SELECT ob.orders_order_id AS order_id, b.title, ob.quantity, ob.price
FROM orders_books ob
JOIN books b ON b.books_id = ob.books_books_id
WHERE ob.orders_order_id = 1;

-- 3) Find suppliers for a book
SELECT b.title, s.supplier_name
FROM suppliers_books sb
JOIN books b ON b.books_id = sb.books_books_id
JOIN suppliers s ON s.suppliers_id = sb.suppliers_suppliers_id
WHERE b.books_id = 1;
