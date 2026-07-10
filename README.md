**Here is the complete step-by-step solution for all 10 questions in one response.**

I will:
1. Create a database `mydb` (or use appropriate DB per question).
2. Create collections with sample data where needed.
3. Provide full MongoDB shell commands (`mongosh` / `mongo`) for each part.
4. Execute one by one from start.

---

### **1. Where Clause, AND, OR + CRUD Operations**

```javascript
use mydb

// Create collection and insert sample data
db.createCollection("employees")

db.employees.insertMany([
  { _id: 1, name: "Alice", age: 30, dept: "IT", salary: 70000, active: true },
  { _id: 2, name: "Bob", age: 25, dept: "HR", salary: 50000, active: true },
  { _id: 3, name: "Charlie", age: 35, dept: "IT", salary: 80000, active: false },
  { _id: 4, name: "Diana", age: 28, dept: "Finance", salary: 65000, active: true }
])

// a. WHERE, AND, OR
db.employees.find({ dept: "IT" })                          // Where clause
db.employees.find({ dept: "IT", age: { $gt: 28 } })       // AND
db.employees.find({ $or: [{ dept: "IT" }, { dept: "HR" }] }) // OR

// b. CRUD
db.employees.insertOne({ _id: 5, name: "Eve", age: 32, dept: "IT", salary: 75000 })

db.employees.find({ name: "Alice" })                       // Query
db.employees.updateOne({ name: "Bob" }, { $set: { salary: 55000 } })
db.employees.deleteOne({ name: "Charlie" })

// Projection
db.employees.find({}, { name: 1, dept: 1, _id: 0 })
```

---

### **2. Projection + Limit**

```javascript
// Continue in mydb
// a. Select specific fields (Projection)
db.employees.find({}, { name: 1, age: 1, dept: 1, _id: 0 })

// b. First 5 documents with limit
db.employees.find({}, { name: 1, age: 1, dept: 1, _id: 0 }).limit(5)
```

---

### **3. Query Selectors**

```javascript
// Comparison + Logical
db.employees.find({ age: { $gte: 30 } })
db.employees.find({ salary: { $gt: 60000, $lt: 80000 } })
db.employees.find({ $and: [{ dept: "IT" }, { active: true }] })
db.employees.find({ $nor: [{ age: { $lt: 25 } }, { salary: { $gt: 90000 } }] })

// Geospatial (create sample geo data)
db.places.insertMany([
  { name: "Park", location: { type: "Point", coordinates: [ -73.97, 40.77 ] } },
  { name: "Museum", location: { type: "Point", coordinates: [ -73.99, 40.75 ] } }
])

db.places.createIndex({ location: "2dsphere" })
db.places.find({
  location: {
    $near: {
      $geometry: { type: "Point", coordinates: [ -73.98, 40.76 ] },
      $maxDistance: 1000
    }
  }
})

// Bitwise (example)
db.employees.find({ _id: { $bitsAllSet: 1 } })  // Adjust based on your data
```

---

### **4. Projection Operators ($ , $elemMatch, $slice)**

```javascript
db.createCollection("students")

db.students.insertMany([
  { name: "John", scores: [85, 90, 78], courses: ["Math", "Physics", "Chem"] },
  { name: "Emma", scores: [92, 88, 95], courses: ["Bio", "Math"] }
])

// $ projection (first matching element)
db.students.find({ scores: 90 }, { "scores.$": 1, name: 1 })

// $elemMatch
db.students.find(
  { scores: { $elemMatch: { $gte: 90 } } },
  { name: 1, scores: { $elemMatch: { $gte: 90 } } }
)

// $slice
db.students.find({}, { name: 1, scores: { $slice: 2 }, courses: { $slice: -2 } })
```

---

### **5. Aggregation Operators**

```javascript
db.createCollection("sales")

db.sales.insertMany([
  { item: "Laptop", price: 1200, quantity: 2, category: "Electronics" },
  { item: "Phone", price: 800, quantity: 5, category: "Electronics" },
  { item: "Book", price: 20, quantity: 10, category: "Books" }
])

db.sales.aggregate([
  { $group: {
      _id: "$category",
      totalRevenue: { $sum: { $multiply: ["$price", "$quantity"] } },
      avgPrice: { $avg: "$price" },
      minPrice: { $min: "$price" },
      maxPrice: { $max: "$price" },
      items: { $push: "$item" },
      uniqueItems: { $addToSet: "$item" }
    }
  }
])
```

---

### **6. Aggregation Pipeline ($match, $group, $sort, $project, $skip, etc.)**

```javascript
db.sales.aggregate([
  { $match: { category: "Electronics" } },           // Filter
  { $group: {
      _id: "$item",
      totalSales: { $sum: { $multiply: ["$price","$quantity"] } },
      count: { $sum: 1 }
    }
  },
  { $sort: { totalSales: -1 } },                     // Sort descending
  { $project: { item: "$_id", revenue: "$totalSales", _id: 0 } },
  { $skip: 0 },                                      // Skip (pagination)
  { $limit: 10 }
])
```

---

### **7. Listings & Reviews + E-commerce**

```javascript
// 7a - ListingsAndReviews (sample)
db.createCollection("listingsAndReviews")

db.listingsAndReviews.insertMany([
  {
    listing_url: "http://example.com/1",
    name: "Cozy Apartment",
    address: { street: "123 Main St" },
    host: { host_picture_url: "https://example.com/host1.jpg" }
  },
  {
    listing_url: "http://example.com/2",
    name: "Luxury Villa",
    address: { street: "456 Ocean Ave" },
    host: { host_picture_url: null }
  }
])

db.listingsAndReviews.find(
  { "host.host_picture_url": { $exists: true, $ne: null } },
  { listing_url: 1, name: 1, address: 1, "host.host_picture_url": 1 }
)

// 7b - E-commerce reviews summary
db.createCollection("reviews")

db.reviews.insertMany([
  { product: "Laptop", rating: 5, review: "Excellent" },
  { product: "Laptop", rating: 4, review: "Good" },
  { product: "Phone", rating: 3, review: "Average" }
])

db.reviews.aggregate([
  { $group: {
      _id: "$product",
      avgRating: { $avg: "$rating" },
      reviewCount: { $sum: 1 },
      reviews: { $push: "$review" }
    }
  }
])
```

---

### **8. Indexes**

```javascript
// a. Different indexes
db.employees.createIndex({ email: 1 }, { unique: true })
db.employees.createIndex({ age: 1 }, { sparse: true })
db.employees.createIndex({ dept: 1, salary: -1 })           // Compound
db.employees.createIndex({ tags: 1 })                        // Multikey (if tags array)

// b. Query optimization demo
db.employees.find({ dept: "IT", salary: { $gt: 60000 } }).explain("executionStats")
```

---

### **9. Text Search**

```javascript
db.createCollection("catalog")

db.catalog.insertMany([
  { name: "Wireless Headphones", description: "Noise cancelling bluetooth headphones" },
  { name: "Smart Watch", description: "Fitness tracking smartwatch with heart rate" },
  { name: "Bluetooth Speaker", description: "Portable waterproof speaker" }
])

db.catalog.createIndex({ name: "text", description: "text" })

// a. Text search
db.catalog.find({ $text: { $search: "bluetooth" } })

// b. Exclude words/phrases
db.catalog.find({ $text: { $search: "headphones -noise" } })
db.catalog.find({ $text: { $search: "\"smart watch\"" } })
```

---

### **10. Text Search Aggregation Pipeline**

```javascript
db.catalog.aggregate([
  { $match: { $text: { $search: "bluetooth" } } },
  { $project: { name: 1, description: 1, score: { $meta: "textScore" } } },
  { $sort: { score: { $meta: "textScore" } } },
  { $limit: 5 }
])
```

---

**How to run all at once:**

1. Start MongoDB server.
2. Open `mongosh`
3. Copy-paste each block one by one.

All code is self-contained with sample data creation. Let me know if you want Python (PyMongo) versions or export to `.js` file!
