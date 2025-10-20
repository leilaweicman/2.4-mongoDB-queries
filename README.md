# S2.04 - MongoDB Queries

## 📚 Project Overview
This repository contains the **solutions for the MongoDB queries exercise (S2.04)**, based on a dataset of New York City restaurants.  
Each query demonstrates a specific MongoDB operator or functionality, focusing on filtering, projection, sorting, and pattern matching.

---

## ⚙️ Dataset
The dataset represents a collection called **`restaurants`**, where each document contains:
- `restaurant_id`
- `name`
- `borough`
- `cuisine`
- `address` (with subfields `building`, `coord`, `street`, `zipcode`)
- `grades` (array of objects with `date`, `grade`, and `score`)

Example:
```json
{
  "restaurant_id": "40357217",
  "name": "Wild Asia",
  "borough": "Bronx",
  "cuisine": "American",
  "address": {
    "building": "2300",
    "coord": [-73.8786113, 40.8502883],
    "street": "Southern Boulevard",
    "zipcode": "10460"
  },
  "grades": [
    { "grade": "A", "score": 11 },
    { "grade": "A", "score": 4 }
  ]
}
```

## 💡 Notes on Implementation
- All queries use the standard syntax:
```js
db.restaurants.find(<filter>, <projection>)
```

- **Alternative valid approaches** are noted below.

### 🔍 Common Variations and Alternatives

| Concept | Example | Alternative | Comment |
|----------|----------|-------------|----------|
| **Regex search** | `{ name: { $regex: /^Wil/, $options: "i" } }` | `{ name: { $regex: /^Wil/i } }` | Both work the same way. `$options` is clearer when patterns are dynamic. |
| **Logical AND** | `{ cuisine: { $ne: "American" }, borough: { $ne: "Brooklyn" } }` | `{ $and: [ { cuisine: { $ne: "American" } }, { borough: { $ne: "Brooklyn" } } ] }` | Implicit AND (first option) is shorter and more readable. |
| **Negation / Exclusion** | `{ borough: { $nin: ["Bronx", "Queens"] } }` | `{ borough: { $not: { $in: ["Bronx", "Queens"] } } }` | `$nin` is preferred; `$not` is rarely needed for simple exclusions. |
| **Type checking** | `{ "address.coord": { $type: "double" } }` | `{ "address.coord": { $type: 1 } }` | Both are valid. The string `"double"` is more readable; `1` is the BSON code. |
| **Sorting results** | `.sort({ name: 1 })` | `aggregate([ { $sort: { name: 1 } } ])` | Use aggregation syntax in environments that don’t allow chaining (e.g., Mongo Playground). |
| **Regex case-insensitivity** | `{ name: { $regex: "mon", $options: "i" } }` | `{ name: { $regex: /mon/i } }` | Identical result — the first is JSON-friendly for scripts. |
| **Check for missing or empty fields** | `{ "address.street": { $exists: false } }` | `{ $or: [ { "address.street": { $exists: false } }, { "address.street": "" } ] }` | Combine both to catch missing or empty values. |
| **Alternative comparison syntax** | `{ "grades.score": { $gt: 70, $lt: 100 } }` | `{ $and: [ { "grades.score": { $gt: 70 } }, { "grades.score": { $lt: 100 } } ] }` | Both work, but chaining in one object is cleaner. |


## 🧠 Testing Environment

All queries were tested using **[OneCompiler MongoDB Online](https://onecompiler.com/mongodb)**,  
a free, browser-based MongoDB environment that supports `.find()`, `.sort()`, `.limit()`, `.skip()`, `$regex`, and most common operators.

It allows importing and inserting documents directly using `insertMany()`,  
making it ideal for educational exercises without requiring a local MongoDB installation.
