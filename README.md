# Mongoose Getting Started

A beginner-friendly Node.js project demonstrating how to use [Mongoose](https://mongoosejs.com/) for MongoDB database operations. This repository showcases essential CRUD (Create, Read, Update, Delete) operations with schema validation and embedded documents.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Project Structure](#project-structure)
- [Code Overview](#code-overview)
  - [Database Connection](#database-connection)
  - [Schema Definition](#schema-definition)
  - [CRUD Operations](#crud-operations)
- [Best Practices](#best-practices)
- [Running the Application](#running-the-application)
- [License](#license)

## Prerequisites

Before running this project, make sure you have the following installed:

- [Node.js](https://nodejs.org/) (v14 or higher recommended)
- [MongoDB](https://www.mongodb.com/try/download/community) (running locally on port 27017)

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/scars-and-screws/mongoos.git
   cd mongoos
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Ensure MongoDB is running locally:
   ```bash
   mongod
   ```

## Project Structure

```
mongoos/
├── app.js          # Main application file with Mongoose examples
├── package.json    # Project dependencies and metadata
├── .gitignore      # Git ignore rules
└── README.md       # Project documentation
```

## Code Overview

### Database Connection

The application connects to a local MongoDB instance:

```javascript
const mongoose = require('mongoose');
mongoose.connect("mongodb://localhost:27017/fruitsDB");
```

### Schema Definition

#### Fruit Schema

Defines a schema with validation rules:

```javascript
const fruitSchema = new mongoose.Schema({
    name: {
        type: String,
        required: [true, 'Please check your entry!']
    },
    rating: {
        type: Number,
        min: 1,
        max: 10
    },
    review: String
});
```

#### Person Schema (with Embedded Document)

Demonstrates embedding one schema within another:

```javascript
const personSchema = new mongoose.Schema({
    name: String,
    age: Number,
    favoriteFruit: fruitSchema
});
```

### CRUD Operations

| Operation | Method | Example |
|-----------|--------|---------|
| **Create** | `save()` | `fruit.save()` |
| **Create Many** | `insertMany()` | `Fruit.insertMany([kiwi, orange, banana])` |
| **Read** | `find()` | `Fruit.find(callback)` |
| **Update** | `updateOne()` | `Fruit.updateOne({_id: "..."}, {name: "Peach"})` |
| **Delete One** | `deleteOne()` | `Fruit.deleteOne({name: "Peach"})` |
| **Delete Many** | `deleteMany()` | `Person.deleteMany({name: "John"})` |

## Best Practices

### 1. Schema Design

- **Use explicit types**: Always define field types explicitly (`String`, `Number`, `Boolean`, etc.)
- **Add validation**: Use built-in validators like `required`, `min`, `max`, `enum`, and custom validators
- **Use meaningful names**: Choose descriptive names for schemas, models, and fields

```javascript
// Good practice
const fruitSchema = new mongoose.Schema({
    name: {
        type: String,
        required: [true, 'Name is required'],
        trim: true
    },
    rating: {
        type: Number,
        min: [1, 'Rating must be at least 1'],
        max: [10, 'Rating cannot exceed 10']
    }
});
```

### 2. Connection Management

- **Handle connection events**: Listen for connection success and error events
- **Use environment variables**: Store connection strings in environment variables for security

```javascript
// Recommended connection pattern
mongoose.connect(process.env.MONGODB_URI || "mongodb://localhost:27017/fruitsDB")
    .then(() => console.log('Connected to MongoDB'))
    .catch(err => console.error('Connection error:', err));
```

### 3. Error Handling

- **Always handle errors**: Use try-catch blocks or error callbacks
- **Provide meaningful error messages**: Include context in error messages

```javascript
// Using async/await with error handling
async function findFruits() {
    try {
        const fruits = await Fruit.find();
        console.log(fruits);
    } catch (error) {
        console.error('Error finding fruits:', error.message);
    }
}
```

### 4. Security Best Practices

- **Never hardcode credentials**: Use environment variables for sensitive data
- **Validate user input**: Always validate and sanitize data before saving
- **Use parameterized queries**: Mongoose handles this automatically, but avoid string concatenation in queries

### 5. Performance Tips

- **Create indexes**: Add indexes for frequently queried fields
- **Use lean queries**: Use `.lean()` for read-only operations to get plain JavaScript objects
- **Limit and paginate**: Always limit query results for large collections

```javascript
// Efficient query with pagination
const fruits = await Fruit.find()
    .limit(10)
    .skip(0)
    .lean();
```

### 6. Code Organization

- **Separate concerns**: Keep schemas, models, and routes in separate files
- **Use model files**: Create dedicated files for each model

```
models/
├── Fruit.js
├── Person.js
└── index.js
```

### 7. Graceful Shutdown

- **Close connections properly**: Always close the database connection when the app terminates

```javascript
process.on('SIGINT', async () => {
    await mongoose.connection.close();
    console.log('MongoDB connection closed');
    process.exit(0);
});
```

## Running the Application

Start the application with:

```bash
node app.js
```

This will:
1. Connect to the local MongoDB instance
2. Create sample fruit and person documents
3. Display the names of all fruits in the database
4. Disconnect after 1 second

## License

This project is licensed under the ISC License.
