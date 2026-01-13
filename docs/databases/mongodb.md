# MongoDB

MongoDB is a popular NoSQL document database that stores data in flexible, JSON-like documents.

## Installation

### Using Docker

```bash
docker run -d \
  --name mongodb \
  -p 27017:27017 \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=password \
  mongo:latest
```

### Local Installation

```bash
# Ubuntu/Debian
sudo apt-get install mongodb

# macOS
brew tap mongodb/brew
brew install mongodb-community
```

## Connection

### Using MongoDB Driver (Node.js)

```javascript
const { MongoClient } = require('mongodb');

const uri = 'mongodb://admin:password@localhost:27017';
const client = new MongoClient(uri);

async function connect() {
  await client.connect();
  const db = client.db('mydatabase');
  return db;
}
```

### Using Mongoose (ORM)

```javascript
const mongoose = require('mongoose');

await mongoose.connect('mongodb://admin:password@localhost:27017/mydatabase');
```

## CRUD Operations

### Create Documents

```javascript
const users = db.collection('users');

// Insert one
await users.insertOne({
  name: 'John Doe',
  email: 'john@example.com',
  age: 30
});

// Insert many
await users.insertMany([
  { name: 'Jane Doe', email: 'jane@example.com', age: 25 },
  { name: 'Bob Smith', email: 'bob@example.com', age: 35 }
]);
```

### Read Documents

```javascript
// Find one
const user = await users.findOne({ email: 'john@example.com' });

// Find many
const allUsers = await users.find({}).toArray();

// Find with filter
const adults = await users.find({ age: { $gte: 18 } }).toArray();

// Find with projection (select specific fields)
const names = await users.find({}, { projection: { name: 1, _id: 0 } }).toArray();
```

### Update Documents

```javascript
// Update one
await users.updateOne(
  { email: 'john@example.com' },
  { $set: { age: 31 } }
);

// Update many
await users.updateMany(
  { age: { $lt: 18 } },
  { $set: { isMinor: true } }
);

// Upsert (update or insert)
await users.updateOne(
  { email: 'new@example.com' },
  { $set: { name: 'New User' } },
  { upsert: true }
);
```

### Delete Documents

```javascript
// Delete one
await users.deleteOne({ email: 'john@example.com' });

// Delete many
await users.deleteMany({ age: { $lt: 18 } });
```

## Query Operators

### Comparison Operators

```javascript
// Equal
{ age: 30 }

// Greater than
{ age: { $gt: 18 } }

// Greater than or equal
{ age: { $gte: 18 } }

// Less than
{ age: { $lt: 65 } }

// In array
{ status: { $in: ['active', 'pending'] } }

// Not equal
{ status: { $ne: 'inactive' } }
```

### Logical Operators

```javascript
// AND
{ $and: [{ age: { $gte: 18 } }, { age: { $lt: 65 } }] }

// OR
{ $or: [{ status: 'active' }, { role: 'admin' }] }

// NOT
{ age: { $not: { $lt: 18 } } }
```

### Array Operators

```javascript
// Element in array
{ tags: 'javascript' }

// All elements in array
{ tags: { $all: ['javascript', 'nodejs'] } }

// Array size
{ tags: { $size: 3 } }
```

## Indexing

### Create Indexes

```javascript
// Single field index
await users.createIndex({ email: 1 });

// Compound index
await users.createIndex({ lastName: 1, firstName: 1 });

// Unique index
await users.createIndex({ email: 1 }, { unique: true });

// Text index (for search)
await users.createIndex({ name: 'text', bio: 'text' });
```

### List Indexes

```javascript
const indexes = await users.indexes();
console.log(indexes);
```

### Drop Index

```javascript
await users.dropIndex('email_1');
```

## Aggregation Pipeline

```javascript
const results = await users.aggregate([
  // Stage 1: Match documents
  { $match: { age: { $gte: 18 } } },
  
  // Stage 2: Group by field
  { $group: {
    _id: '$country',
    averageAge: { $avg: '$age' },
    count: { $sum: 1 }
  }},
  
  // Stage 3: Sort results
  { $sort: { count: -1 } },
  
  // Stage 4: Limit results
  { $limit: 10 }
]).toArray();
```

## Mongoose Schema

```javascript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    trim: true
  },
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true
  },
  age: {
    type: Number,
    min: 0,
    max: 120
  },
  role: {
    type: String,
    enum: ['user', 'admin', 'moderator'],
    default: 'user'
  },
  createdAt: {
    type: Date,
    default: Date.now
  }
});

const User = mongoose.model('User', userSchema);

// Usage
const user = new User({
  name: 'John Doe',
  email: 'john@example.com',
  age: 30
});

await user.save();
```

## Transactions

```javascript
const session = client.startSession();

try {
  await session.withTransaction(async () => {
    await users.insertOne({ name: 'John' }, { session });
    await orders.insertOne({ userId: 1, amount: 100 }, { session });
  });
} finally {
  await session.endSession();
}
```

## Best Practices

### Connection Management

```javascript
// Create client once and reuse
let cachedClient = null;

async function connectToDatabase() {
  if (cachedClient) {
    return cachedClient;
  }

  const client = await MongoClient.connect(uri, {
    maxPoolSize: 10
  });

  cachedClient = client;
  return client;
}
```

### Schema Design

- Embed related data that is accessed together
- Use references for data that is accessed independently
- Avoid deep nesting (keep it under 3-4 levels)
- Use arrays for one-to-few relationships
- Use references for one-to-many or many-to-many

### Performance

```javascript
// Use projection to limit returned fields
await users.findOne({ _id: 1 }, { projection: { name: 1, email: 1 } });

// Use lean() with Mongoose for faster queries
await User.find({}).lean();

// Create indexes on frequently queried fields
await users.createIndex({ email: 1 });
```

## Docker Compose

```yaml
version: '3.8'

services:
  mongodb:
    image: mongo:latest
    container_name: mongodb
    restart: always
    ports:
      - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password
    volumes:
      - mongodb_data:/data/db

volumes:
  mongodb_data:
```

