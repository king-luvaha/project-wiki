## Getting Started with MongoDB Compass - Part 1
### 🧱 What is MongoDB and What is the Query API?

**MongoDB** is a **NoSQL** database that stores data in flexible, JSON-like documents (called BSON). Instead of tables and rows (like SQL), MongoDB works with collections and documents.

The **Query API** is how you search and manipulate data inside MongoDB. Think of it as asking questions like:

* “Find me all users who are older than 18.”
* “Update the status of all completed tasks.”
* “Delete inactive accounts.”

---

### 🛠 Step 1: Set Up MongoDB & MongoDB Compass

Since you mentioned MongoDB Compass is already installed and using default settings, let’s ensure you're connected:

1. **Open MongoDB Compass.**
2. Use the default connection string:

   ```
   mongodb://localhost:27017
   ```

   Just click **“Connect”**.
3. You’ll now see your databases (e.g., `admin`, `local`, and maybe some custom ones).

To start testing queries:

* Click **“Create Database”** → Name: `testdb`, Collection: `users`.

---

### 📥 Step 2: Insert Sample Data

Click your new collection: `testdb > users` and go to the **“Documents” tab**.

Click **“Insert Document”** and paste this:

```json
{
  "name": "Ezra",
  "age": 25,
  "location": "Nairobi",
  "status": "active"
}
```

Repeat and add a few more:

```json
{ "name": "Jane", "age": 17, "location": "Kisumu", "status": "inactive" }
{ "name": "Mark", "age": 32, "location": "Mombasa", "status": "active" }
{ "name": "Ali", "age": 22, "location": "Nairobi", "status": "inactive" }
```

---

### 🔍 Step 3: Query Data with the Query API (Visually!)

In Compass, go to the **“Filter”** field — this is where we use the Query API!

---

### ✅ Find a specific document

**Goal:** Get user with name `"Ezra"`
**Query:**

```json
{ "name": "Ezra" }
```

➡️ Press Enter. You’ll see Ezra’s document appear.

---

### 🔢 Filter with Comparison Operators

**Goal:** Find users aged **over 20**

```json
{ "age": { "$gt": 20 } }
```

* `$gt` = greater than
  Other operators:
* `$lt` (less than)
* `$gte` (greater than or equal)
* `$lte` (less than or equal)

---

### 🧠 Use Logical Operators

**Goal:** Get users from Nairobi **or** Kisumu

```json
{
  "$or": [
    { "location": "Nairobi" },
    { "location": "Kisumu" }
  ]
}
```

* `$or` combines multiple conditions.
* `$and` would require both conditions to be true.

---

### 🔍 Search with Regex (Pattern Matching)

**Goal:** Find names starting with `"J"`:

```json
{ "name": { "$regex": "^J", "$options": "i" } }
```

* `^` means “starts with”
* `"i"` = case-insensitive

---

### ✅ Combine Conditions

**Goal:** Find active users over 20:

```json
{
  "$and": [
    { "status": "active" },
    { "age": { "$gt": 20 } }
  ]
}
```

---

### ✏️ Step 4: Update Documents

Click **“Documents” > Select a document** > Click **“Edit”**.

Change `"status": "inactive"` to `"status": "active"` — then click **“Update”**.

For bulk updates, you'd typically use the **Mongo Shell** or **Node.js**, but Compass supports this in **“Aggregations”** and **“Command Line”** tabs too (advanced).

---

### 🗑 Step 5: Delete Documents

In the document view:

* Click the **trash icon** to delete a single document.
* For mass deletion, you can use the **Filter**, then **“Delete Many”** based on your filter.

For example, to delete all users under 18:

```json
{ "age": { "$lt": 18 } }
```

Then click **“DELETE”**.

---

### 🧮 Bonus: Aggregation Tab

You can run advanced queries like group, count, and summarize data using the **Aggregation Pipeline**.

**Example:** Count users per location:

1. Go to **“Aggregations”**
2. Add this stage:

```js
{ "$group": { "_id": "$location", "total": { "$sum": 1 } } }
```

Then click **“Execute”**.

---

### 🧠 Recap: Common MongoDB Query API Operators

| Operator      | Use Case              |
| ------------- | --------------------- |
| `$gt`, `$lt`  | Compare numbers       |
| `$eq`, `$ne`  | Equal / Not equal     |
| `$or`, `$and` | Logic                 |
| `$regex`      | Pattern match         |
| `$in`, `$nin` | Check inside an array |
| `$exists`     | Field exists or not   |

---

### 🚀 What Next?

You now know how to:

* Add documents
* Query like a pro with filters
* Use logical and comparison operators
* Update or delete data — all visually

As you advance, you’ll want to:

* Learn the Mongo Shell
* Use MongoDB with Node.js, Python, or Django
* Write aggregation pipelines
* Design proper schemas

---

### 💡 Pro Tip:

If you're building projects, MongoDB Atlas offers free cloud databases so you can access your data remotely.

---


## 💻 Getting Started with the MongoDB Query API Using MongoDB Shell (mongosh) - Part 2

### 🧱 What is MongoDB and What is the Query API?

**MongoDB** is a **NoSQL** database that stores data in flexible, JSON-like documents (called BSON). Instead of tables and rows (like SQL), MongoDB works with collections and documents.

The **Query API** is how you search and manipulate data inside MongoDB. Think of it as asking questions like:

* “Find me all users who are older than 18.”
* “Update the status of all completed tasks.”
* “Delete inactive accounts.”

---

### 🔧 Step 1: Open MongoDB Shell

Assuming MongoDB is installed locally:

### 👉 Open your terminal and run:

```bash
mongosh
```

You’ll see a welcome message and be connected to the default `test` database:

```
test>
```

✅ **Tip:** To switch to or create a new database:

```js
use testdb
```

If `testdb` doesn’t exist yet, MongoDB will create it as soon as you insert data.

---

### 🗂 Step 2a: Create a Collection and Insert Documents

#### 🆕 Multi-line Input Method (Perfect for Small Tests)

For small-scale testing, you can use mongosh's multi-line input mode:

1. **Start your insert command**:
   ```js
   db.users.insertMany([
   ```

2. **Press Enter** - you'll see `...` indicating continuation mode

3. **Add your documents line by line**:
   ```js
     { name: "Ezra", age: 25, location: "Nairobi", status: "active" },
     { name: "Jane", age: 17, location: "Kisumu", status: "inactive" },
   ```

4. **Complete the array and command**:
   ```js
     { name: "Mark", age: 32, location: "Mombasa", status: "active" },
     { name: "Ali", age: 22, location: "Nairobi", status: "inactive" }
   ])
   ```

5. **Press Enter twice** to execute (first Enter ends the last line, second executes)

✅ **Pro Tip:** If you make a mistake:
- Press Ctrl+C to cancel the multi-line input
- Or type `exit` and press Enter to abort

#### Alternative Insert Methods

For larger datasets, consider:
- **Editor mode** (after configuring an editor)
- **Loading from a file** (`load("insert.js")`)
- **Single-line JSON** (for very small inserts)

---

### 🗂 Step 2b: Create a Collection and Insert Documents

Create a `users` collection and insert documents:

```js
db.users.insertMany([
  { name: "Ezra", age: 25, location: "Nairobi", status: "active" },
  { name: "Jane", age: 17, location: "Kisumu", status: "inactive" },
  { name: "Mark", age: 32, location: "Mombasa", status: "active" },
  { name: "Ali", age: 22, location: "Nairobi", status: "inactive" }
])
```

Output should say:

```
Acknowledged: true, insertedIds: [...]
```

✅ You now have sample data!

---

### 🔍 Step 3: Querying Data (MongoDB Query API in Action)

 1. Find all documents

```js
db.users.find()
```

---

 2. Find a specific document

```js
db.users.find({ name: "Ezra" })
```

---

 3. Comparison queries

```js
db.users.find({ age: { $gt: 20 } })       // age > 20
db.users.find({ age: { $lte: 25 } })      // age ≤ 25
```

---

 4. Logical queries

```js
db.users.find({
  $or: [
    { location: "Nairobi" },
    { location: "Kisumu" }
  ]
})
```

```js
db.users.find({
  $and: [
    { status: "active" },
    { age: { $gt: 20 } }
  ]
})
```

---

5. Regex matching

```js
db.users.find({ name: { $regex: "^J", $options: "i" } })
```

---

6. Projecting specific fields

```js
db.users.find({ age: { $gt: 20 } }, { name: 1, age: 1, _id: 0 })
```

This shows only `name` and `age` fields (excluding `_id`).

---

### ✏️ Step 4: Update Documents

#### Update one document

```js
db.users.updateOne(
  { name: "Jane" },
  { $set: { status: "active" } }
)
```

#### Update many documents

```js
db.users.updateMany(
  { status: "inactive" },
  { $set: { verified: false } }
)
```

#### Upsert (update or insert if not found)

```js
db.users.updateOne(
  { name: "NewUser" },
  { $set: { age: 30, status: "active" } },
  { upsert: true }
)
```

---

### 🗑 Step 5: Delete Documents

#### Delete one

```js
db.users.deleteOne({ name: "Ezra" })
```

#### Delete many

```js
db.users.deleteMany({ status: "inactive" })
```

---

### 🧮 Step 6: Aggregation Example

Let’s say you want to group users by location and count them:

```js
db.users.aggregate([
  { $group: { _id: "$location", total: { $sum: 1 } } }
])
```

Output:

```json
[
  { _id: "Nairobi", total: 2 },
  { _id: "Kisumu", total: 1 },
  { _id: "Mombasa", total: 1 }
]
```

---

### 🔁 Bonus: Other Useful Query Operators

| Operator                      | Description                   |
| ----------------------------- | ----------------------------- |
| `$eq`, `$ne`                  | Equal, Not equal              |
| `$gt`, `$lt`, `$gte`, `$lte`  | Greater/Less than comparisons |
| `$in`, `$nin`                 | Check value in an array       |
| `$exists`                     | Check if a field exists       |
| `$type`                       | Check field type              |
| `$regex`                      | Pattern matching              |
| `$and`, `$or`, `$not`, `$nor` | Logical operators             |

---

#### 🚀 What Next?

Now that you’ve mastered the basics of querying with MongoDB Shell:

* ✅ You know how to connect to a DB
* ✅ Insert, read, update, and delete documents
* ✅ Use logical, comparison, and pattern matching
* ✅ Perform basic aggregation

---

#### 🔗 Want to Practice More?

You can:

* Build a local Node.js or Python project and connect it to your `testdb`
* Explore MongoDB Atlas to practice with cloud-hosted DBs
* Write scripts using `mongosh` to automate queries

---




