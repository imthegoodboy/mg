 
---

### **Program 1a: Illustration of Where Clause, AND, OR operations**

```javascript
use StudentDB

db.students.insertMany([
  { rno: 1, name: "Bhavana", city: "Chennai", marks: 85, age: 20, dept: "CSE" },
  { rno: 2, name: "Ajay", city: "Bangalore", marks: 78, age: 21, dept: "ECE" },
  { rno: 3, name: "Priya", city: "Chennai", marks: 92, age: 20, dept: "CSE" },
  { rno: 4, name: "Rahul", city: "Mysore", marks: 65, age: 22, dept: "MECH" },
  { rno: 5, name: "Sneha", city: "Bangalore", marks: 88, age: 21, dept: "CSE" }
])

db.students.find({city: "Chennai"})
db.students.find({marks: {$gt: 80}})
 
db.students.find({$and: [{city: "Bangalore"}, {marks: {$gt: 75}}]})

 
db.students.find({$or: [{dept: "CSE"}, {marks: {$gte: 90}}]})
```

---

### **Program 1b: Insert, Query, Update, Delete and Projection**

```javascript
use StudentDB

db.students.insertMany([
  { rno: 1, name: "Bhavana", city: "Chennai", marks: 85, age: 20 },
  { rno: 2, name: "Ajay", city: "Bangalore", marks: 78, age: 21 },
  { rno: 3, name: "Priya", city: "Chennai", marks: 92, age: 20 }
])

 
db.students.insertOne({ rno: 4, name: "Kiran", city: "Hyderabad", marks: 76, age: 22 })

 
db.students.find()

 
db.students.updateOne({ rno: 1 }, { $set: { marks: 95 } })

 
db.students.deleteOne({ rno: 2 })

 
db.students.find({}, { name: 1, marks: 1, city: 1, _id: 0 })
```

---

### **Program 2a: Select certain fields and ignore some fields**

```javascript
use StudentDB

db.students.insertMany([
  { rno: 1, name: "Bhavana", city: "Chennai", marks: 85, age: 20, dept: "CSE" },
  { rno: 2, name: "Ajay", city: "Bangalore", marks: 78, age: 21, dept: "ECE" },
  { rno: 3, name: "Priya", city: "Chennai", marks: 92, age: 20, dept: "CSE" },
  { rno: 4, name: "Rahul", city: "Mysore", marks: 65, age: 22, dept: "MECH" }
])

 
db.students.find({}, { name: 1, city: 1, marks: 1, _id: 0 })

 
db.students.find({}, { age: 0, dept: 0, _id: 0 })
```

---

### **Program 2b: Display the first 5 documents using limit**

```javascript
use StudentDB

db.students.insertMany([
  { rno: 1, name: "Bhavana", city: "Chennai", marks: 85 },
  { rno: 2, name: "Ajay", city: "Bangalore", marks: 78 },
  { rno: 3, name: "Priya", city: "Chennai", marks: 92 },
  { rno: 4, name: "Rahul", city: "Mysore", marks: 65 },
  { rno: 5, name: "Sneha", city: "Bangalore", marks: 88 },
  { rno: 6, name: "Kiran", city: "Hyderabad", marks: 76 },
  { rno: 7, name: "Meera", city: "Chennai", marks: 89 }
])

 
db.students.find().limit(5)

 
db.students.find().sort({marks: -1}).limit(5)
```

---

### **Program 3: Query selectors (comparison & logical)**

```javascript
use StudentDB

db.students.insertMany([
  { rno: 1, name: "Bhavana", marks: 85, age: 20, dept: "CSE" },
  { rno: 2, name: "Ajay", marks: 78, age: 21, dept: "ECE" },
  { rno: 3, name: "Priya", marks: 92, age: 20, dept: "CSE" },
  { rno: 4, name: "Rahul", marks: 65, age: 22, dept: "MECH" },
  { rno: 5, name: "Sneha", marks: 88, age: 21, dept: "CSE" },
  { rno: 6, name: "Kiran", marks: 72, age: 23, dept: "ECE" }
])

 
db.students.find({marks: {$gt: 80}})
db.students.find({marks: {$gte: 85}})
db.students.find({marks: {$lt: 75}})
db.students.find({age: {$ne: 20}})

 
db.students.find({$and: [{dept: "CSE"}, {marks: {$gt: 80}}]})
db.students.find({$or: [{dept: "ECE"}, {marks: {$lt: 75}}]})
db.students.find({$nor: [{dept: "CSE"}, {age: {$lt: 21}}]})
```

---

### **Program 4: Projection operators ($ , $elemMatch, $slice)**

```javascript
use StudentDB

db.students.insertMany([
  {
    rno: 101,
    name: "Arjun",
    subjects: ["Math", "Physics", "Chemistry", "Biology"],
    scores: [85, 78, 92, 88]
  },
  {
    rno: 102,
    name: "Divya",
    subjects: ["English", "Math", "History"],
    scores: [90, 82, 76]
  }
])

 
db.students.find({subjects: "Math"}, { "subjects.$": 1, name: 1 })

 
db.students.find(
  { scores: { $elemMatch: { $gt: 85 } } },
  { name: 1, scores: 1 }
)

 
db.students.find({}, { name: 1, subjects: { $slice: 2 }, _id: 0 })
db.students.find({}, { name: 1, subjects: { $slice: -2 }, _id: 0 })
```

---

### **Program 5: Aggregation operations**

```javascript
use StudentDB

db.students.insertMany([
  { name: "Bhavana", marks: 85, age: 20, dept: "CSE" },
  { name: "Ajay", marks: 78, age: 21, dept: "ECE" },
  { name: "Priya", marks: 92, age: 20, dept: "CSE" },
  { name: "Rahul", marks: 65, age: 22, dept: "MECH" },
  { name: "Sneha", marks: 88, age: 21, dept: "CSE" },
  { name: "Kiran", marks: 72, age: 23, dept: "ECE" }
])

 
db.students.aggregate([{ $group: { _id: null, AverageMarks: { $avg: "$marks" }}}])
db.students.aggregate([{ $group: { _id: null, MaxMarks: { $max: "$marks" }}}])
db.students.aggregate([{ $group: { _id: null, MinMarks: { $min: "$marks" }}}])
 
db.students.aggregate([
  { $group: { 
      _id: "$dept", 
      TotalStudents: { $sum: 1 },
      AvgMarks: { $avg: "$marks" },
      HighestMarks: { $max: "$marks" }
  }}
])

 
db.students.aggregate([
  { $group: { 
      _id: "$dept", 
      Students: { $push: "$name" },
      UniqueMarks: { $addToSet: "$marks" }
  }}
])
```

---

 
