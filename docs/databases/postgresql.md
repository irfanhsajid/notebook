# PostgreSQL

PostgreSQL is a powerful, open-source object-relational database system with a strong reputation for reliability, feature robustness, and performance.

## Installation

### Using Docker

```bash
docker run -d \
  --name postgres \
  -p 5432:5432 \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_USER=admin \
  -e POSTGRES_DB=mydatabase \
  postgres:latest
```

### Local Installation

```bash
# Ubuntu/Debian
sudo apt-get install postgresql postgresql-contrib

# macOS
brew install postgresql
```

## Connection

### Using pg (Node.js)

```javascript
const { Pool } = require('pg');

const pool = new Pool({
  user: 'admin',
  host: 'localhost',
  database: 'mydatabase',
  password: 'password',
  port: 5432,
});

// Query
const result = await pool.query('SELECT * FROM users');
console.log(result.rows);
```

### Connection String

```javascript
const pool = new Pool({
  connectionString: 'postgresql://admin:password@localhost:5432/mydatabase'
});
```

## CRUD Operations

### Create Table

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  age INTEGER CHECK (age >= 0),
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

-- Insert and return
INSERT INTO users (name, email, age)
VALUES ('Alice', 'alice@example.com', 28)
RETURNING id, name;
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

-- Update and return
UPDATE users 
SET name = 'John Smith' 
WHERE id = 1
RETURNING *;
```

### Delete Data

```sql
-- Delete specific rows
DELETE FROM users WHERE age < 18;

-- Delete all rows
DELETE FROM users;

-- Delete and return
DELETE FROM users WHERE id = 1 RETURNING *;
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

-- Multiple joins
SELECT u.name, o.amount, p.name AS product
FROM users u
INNER JOIN orders o ON u.id = o.user_id
INNER JOIN products p ON o.product_id = p.id;
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

### Window Functions

```sql
-- Row number
SELECT name, age, 
  ROW_NUMBER() OVER (ORDER BY age DESC) as rank
FROM users;

-- Running total
SELECT date, amount,
  SUM(amount) OVER (ORDER BY date) as running_total
FROM orders;
```

## JSON Support

PostgreSQL has excellent JSON support:

```sql
-- Create table with JSON column
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  attributes JSONB
);

-- Insert JSON data
INSERT INTO products (name, attributes)
VALUES ('Laptop', '{"brand": "Dell", "specs": {"ram": "16GB", "cpu": "i7"}}');

-- Query JSON data
SELECT * FROM products 
WHERE attributes->>'brand' = 'Dell';

-- Query nested JSON
SELECT * FROM products 
WHERE attributes->'specs'->>'ram' = '16GB';

-- Update JSON field
UPDATE products 
SET attributes = jsonb_set(attributes, '{specs,ram}', '"32GB"')
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

-- Partial index
CREATE INDEX idx_active_users ON users(email) WHERE active = true;

-- GIN index for JSON
CREATE INDEX idx_products_attributes ON products USING GIN (attributes);

-- Full-text search index
CREATE INDEX idx_users_name_fulltext ON users USING GIN (to_tsvector('english', name));
```

## Transactions

```sql
BEGIN;

INSERT INTO users (name, email) VALUES ('John', 'john@example.com');
UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;

COMMIT;
-- or ROLLBACK; if something goes wrong
```

### Using pg in Node.js

```javascript
const client = await pool.connect();

try {
  await client.query('BEGIN');
  
  await client.query('INSERT INTO users (name, email) VALUES ($1, $2)', ['John', 'john@example.com']);
  await client.query('UPDATE accounts SET balance = balance - $1 WHERE user_id = $2', [100, 1]);
  await client.query('UPDATE accounts SET balance = balance + $1 WHERE user_id = $2', [100, 2]);
  
  await client.query('COMMIT');
} catch (error) {
  await client.query('ROLLBACK');
  throw error;
} finally {
  client.release();
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
const query = 'SELECT * FROM users WHERE email = $1';
const result = await pool.query(query, [email]);
```

## Common Patterns

### Pagination

```javascript
const page = 1;
const limit = 10;
const offset = (page - 1) * limit;

const result = await pool.query(
  'SELECT * FROM users ORDER BY id LIMIT $1 OFFSET $2',
  [limit, offset]
);
```

### Search

```javascript
const searchTerm = 'john';
const result = await pool.query(
  'SELECT * FROM users WHERE name ILIKE $1',
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

const query = 'INSERT INTO users (name, email) VALUES ($1, $2)';

for (const user of users) {
  await pool.query(query, user);
}

// Better: Use a single query
const values = users.map((_, i) => `($${i * 2 + 1}, $${i * 2 + 2})`).join(',');
const flatUsers = users.flat();
await pool.query(
  `INSERT INTO users (name, email) VALUES ${values}`,
  flatUsers
);
```

## Database Schema Best Practices

### Use Constraints

```sql
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  amount DECIMAL(10, 2) CHECK (amount > 0),
  status VARCHAR(20) DEFAULT 'pending',
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  CONSTRAINT valid_status CHECK (status IN ('pending', 'completed', 'cancelled'))
);
```

### Use Enum Types

```sql
CREATE TYPE user_role AS ENUM ('user', 'admin', 'moderator');

CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  role user_role DEFAULT 'user'
);
```

## Performance Tips

### Use EXPLAIN ANALYZE

```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'john@example.com';
```

### Connection Pooling

```javascript
const pool = new Pool({
  max: 20, // maximum number of connections
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});
```

### Use Prepared Statements

```javascript
const query = {
  name: 'fetch-user',
  text: 'SELECT * FROM users WHERE id = $1',
  values: [1]
};

const result = await pool.query(query);
```

## Docker Compose

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:latest
    container_name: postgres
    restart: always
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydatabase
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

## Useful Commands

```sql
-- List all databases
\l

-- Connect to database
\c mydatabase

-- List all tables
\dt

-- Describe table
\d users

-- List all indexes
\di

-- Show query execution time
\timing
```

