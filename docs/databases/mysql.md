# MySQL

MySQL is one of the most popular open-source relational database management systems, widely used for web applications.

## Installation

### Using Docker

```bash
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=rootpassword \
  -e MYSQL_DATABASE=mydatabase \
  -e MYSQL_USER=admin \
  -e MYSQL_PASSWORD=password \
  mysql:latest
```

### Local Installation

```bash
# Ubuntu/Debian
sudo apt-get install mysql-server

# macOS
brew install mysql
```

## Connection

### Using mysql2 (Node.js)

```javascript
const mysql = require('mysql2/promise');

const connection = await mysql.createConnection({
  host: 'localhost',
  user: 'admin',
  password: 'password',
  database: 'mydatabase'
});

// Query
const [rows] = await connection.execute('SELECT * FROM users');
console.log(rows);
```

### Connection Pool

```javascript
const pool = mysql.createPool({
  host: 'localhost',
  user: 'admin',
  password: 'password',
  database: 'mydatabase',
  waitForConnections: true,
  connectionLimit: 10,
  queueLimit: 0
});

const [rows] = await pool.execute('SELECT * FROM users');
```

## CRUD Operations

### Create Table

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  age INT CHECK (age >= 0),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Insert Data

```sql
-- Insert single row
INSERT INTO users (name, email, age)
VALUES ('John Doe', 'john@example.com', 30);

-- Insert multiple rows
INSERT INTO users (name, email, age)
VALUES 
  ('Jane Doe', 'jane@example.com', 25),
  ('Bob Smith', 'bob@example.com', 35);

-- Insert and get ID
INSERT INTO users (name, email, age)
VALUES ('Alice', 'alice@example.com', 28);
SELECT LAST_INSERT_ID();
```

### Select Data

```sql
-- Select all
SELECT * FROM users;

-- Select specific columns
SELECT name, email FROM users;

-- Where clause
SELECT * FROM users WHERE age > 25;

-- Order by
SELECT * FROM users ORDER BY age DESC;

-- Limit and offset
SELECT * FROM users LIMIT 10 OFFSET 20;

-- Aggregate functions
SELECT COUNT(*), AVG(age), MAX(age) FROM users;
```

### Update Data

```sql
-- Update single row
UPDATE users 
SET age = 31 
WHERE email = 'john@example.com';

-- Update multiple rows
UPDATE users 
SET age = age + 1 
WHERE age < 30;
```

### Delete Data

```sql
-- Delete specific rows
DELETE FROM users WHERE age < 18;

-- Delete all rows
DELETE FROM users;

-- Truncate (faster, resets auto-increment)
TRUNCATE TABLE users;
```

## Advanced Queries

### Joins

```sql
-- Inner join
SELECT u.name, o.amount
FROM users u
INNER JOIN orders o ON u.id = o.user_id;

-- Left join
SELECT u.name, o.amount
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;

-- Right join
SELECT u.name, o.amount
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
```

### Subqueries

```sql
-- Subquery in WHERE
SELECT * FROM users 
WHERE id IN (SELECT user_id FROM orders WHERE amount > 100);

-- Subquery in FROM
SELECT avg_age FROM (
  SELECT AVG(age) as avg_age FROM users
) AS subquery;
```

### Group By

```sql
SELECT country, COUNT(*) as user_count, AVG(age) as avg_age
FROM users
GROUP BY country
HAVING COUNT(*) > 10
ORDER BY user_count DESC;
```

## JSON Support (MySQL 5.7+)

```sql
-- Create table with JSON column
CREATE TABLE products (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  attributes JSON
);

-- Insert JSON data
INSERT INTO products (name, attributes)
VALUES ('Laptop', '{"brand": "Dell", "specs": {"ram": "16GB", "cpu": "i7"}}');

-- Query JSON data
SELECT * FROM products 
WHERE JSON_EXTRACT(attributes, '$.brand') = 'Dell';

-- Shorter syntax
SELECT * FROM products 
WHERE attributes->>'$.brand' = 'Dell';

-- Update JSON field
UPDATE products 
SET attributes = JSON_SET(attributes, '$.specs.ram', '32GB')
WHERE id = 1;
```

## Indexes

```sql
-- Create index
CREATE INDEX idx_users_email ON users(email);

-- Unique index
CREATE UNIQUE INDEX idx_users_email ON users(email);

-- Composite index
CREATE INDEX idx_users_name_age ON users(name, age);

-- Full-text search index
CREATE FULLTEXT INDEX idx_users_name ON users(name);

-- Show indexes
SHOW INDEX FROM users;

-- Drop index
DROP INDEX idx_users_email ON users;
```

## Transactions

```sql
START TRANSACTION;

INSERT INTO users (name, email) VALUES ('John', 'john@example.com');
UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;

COMMIT;
-- or ROLLBACK; if something goes wrong
```

### Using mysql2 in Node.js

```javascript
const connection = await pool.getConnection();

try {
  await connection.beginTransaction();
  
  await connection.execute('INSERT INTO users (name, email) VALUES (?, ?)', ['John', 'john@example.com']);
  await connection.execute('UPDATE accounts SET balance = balance - ? WHERE user_id = ?', [100, 1]);
  await connection.execute('UPDATE accounts SET balance = balance + ? WHERE user_id = ?', [100, 2]);
  
  await connection.commit();
} catch (error) {
  await connection.rollback();
  throw error;
} finally {
  connection.release();
}
```

## Parameterized Queries

❌ **Bad** (SQL Injection vulnerable):
```javascript
const email = req.body.email;
const query = `SELECT * FROM users WHERE email = '${email}'`;
```

✅ **Good**:
```javascript
const email = req.body.email;
const [rows] = await pool.execute('SELECT * FROM users WHERE email = ?', [email]);
```

## Common Patterns

### Pagination

```javascript
const page = 1;
const limit = 10;
const offset = (page - 1) * limit;

const [rows] = await pool.execute(
  'SELECT * FROM users ORDER BY id LIMIT ? OFFSET ?',
  [limit, offset]
);
```

### Search

```javascript
const searchTerm = 'john';
const [rows] = await pool.execute(
  'SELECT * FROM users WHERE name LIKE ?',
  [`%${searchTerm}%`]
);
```

### Bulk Insert

```javascript
const users = [
  ['John', 'john@example.com'],
  ['Jane', 'jane@example.com'],
  ['Bob', 'bob@example.com']
];

const [result] = await pool.query(
  'INSERT INTO users (name, email) VALUES ?',
  [users]
);
```

## Database Schema Best Practices

### Foreign Keys

```sql
CREATE TABLE orders (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT NOT NULL,
  amount DECIMAL(10, 2) CHECK (amount > 0),
  status ENUM('pending', 'completed', 'cancelled') DEFAULT 'pending',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

### Enum Types

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  role ENUM('user', 'admin', 'moderator') DEFAULT 'user'
);
```

## Performance Tips

### Use EXPLAIN

```sql
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';
```

### Connection Pooling

```javascript
const pool = mysql.createPool({
  connectionLimit: 10,
  host: 'localhost',
  user: 'admin',
  password: 'password',
  database: 'mydatabase'
});
```

### Use Prepared Statements

```javascript
const [rows] = await pool.execute(
  'SELECT * FROM users WHERE id = ?',
  [userId]
);
```

## Useful Commands

```sql
-- Show all databases
SHOW DATABASES;

-- Use database
USE mydatabase;

-- Show all tables
SHOW TABLES;

-- Describe table
DESCRIBE users;

-- Show create table statement
SHOW CREATE TABLE users;

-- Show table status
SHOW TABLE STATUS;

-- Show processlist
SHOW PROCESSLIST;
```

## Backup and Restore

### Backup

```bash
# Backup single database
mysqldump -u admin -p mydatabase > backup.sql

# Backup all databases
mysqldump -u admin -p --all-databases > all_backup.sql

# Backup specific tables
mysqldump -u admin -p mydatabase users orders > tables_backup.sql
```

### Restore

```bash
# Restore database
mysql -u admin -p mydatabase < backup.sql

# Restore all databases
mysql -u admin -p < all_backup.sql
```

## Docker Compose

```yaml
version: '3.8'

services:
  mysql:
    image: mysql:latest
    container_name: mysql
    restart: always
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: mydatabase
      MYSQL_USER: admin
      MYSQL_PASSWORD: password
    volumes:
      - mysql_data:/var/lib/mysql
    command: --default-authentication-plugin=mysql_native_password

volumes:
  mysql_data:
```

## Common Data Types

```sql
-- Numeric
INT, BIGINT, DECIMAL(10,2), FLOAT, DOUBLE

-- String
VARCHAR(255), TEXT, CHAR(10), LONGTEXT

-- Date/Time
DATE, DATETIME, TIMESTAMP, TIME

-- Binary
BLOB, LONGBLOB

-- JSON
JSON

-- Enum
ENUM('value1', 'value2', 'value3')
```

