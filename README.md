// Basic Queries
db.books.find({ genre: "Fiction" })
db.books.find({ published_year: { $gt: 2010 } })
db.books.find({ author: "Paulo Coelho" })
db.books.updateOne({ title: "The Alchemist" }, { $set: { price: 14.99 } })
db.books.deleteOne({ title: "The Alchemist" })

// Advanced Queries
db.books.find({ in_stock: true, published_year: { $gt: 2010 } })
db.books.find({}, { title: 1, author: 1, price: 1, _id: 0 })
db.books.find().sort({ price: 1 })
db.books.find().sort({ price: -1 })
db.books.find().skip(0).limit(5)
db.books.find().skip(5).limit(5)

// Aggregation Pipelines
db.books.aggregate([
  { $group: { _id: "$genre", avg_price: { $avg: "$price" } } }
])
db.books.aggregate([
  { $group: { _id: "$author", total_books: { $sum: 1 } } },
  { $sort: { total_books: -1 } },
  { $limit: 1 }
])
db.books.aggregate([
  {
    $group: {
      _id: {
        $concat: [
          { $toString: { $multiply: [{ $floor: { $divide: ["$published_year", 10] } }, 10] } },
          "s"
        ]
      },
      count: { $sum: 1 }
    }
  },
  { $sort: { _id: 1 } }
])

// Indexing
db.books.createIndex({ title: 1 })
db.books.createIndex({ author: 1, published_year: -1 })
db.books.find({ title: "The Alchemist" }).explain("executionStats")


// 1. Find all books in a specific genre
db.books.find({ genre: "Fiction" })

// 2. Find books published after a certain year
db.books.find({ published_year: { $gt: 2000 } })

// 3. Find books by a specific author
db.books.find({ author: "Paulo Coelho" })

// 4. Update the price of a specific book
db.books.updateOne({ title: "The Alchemist" }, { $set: { price: 12.99 } })

// 5. Delete a book by its title
db.books.deleteOne({ title: "The Alchemist" })


// 1. Find books in stock and published after 2010
db.books.find({ in_stock: true, published_year: { $gt: 2010 } })

// 2. Projection - return only title, author, and price
db.books.find({}, { title: 1, author: 1, price: 1, _id: 0 })

// 3. Sort by price ascending
db.books.find().sort({ price: 1 })

// 4. Sort by price descending
db.books.find().sort({ price: -1 })

// 5. Pagination (5 books per page) - Page 1
db.books.find().skip(0).limit(5)

// Page 2
db.books.find().skip(5).limit(5)


// 1. Average price of books by genre
db.books.aggregate([
  { $group: { _id: "$genre", avgPrice: { $avg: "$price" } } }
])

// 2. Author with most books
db.books.aggregate([
  { $group: { _id: "$author", count: { $sum: 1 } } },
  { $sort: { count: -1 } },
  { $limit: 1 }
])

// 3. Group by publication decade
db.books.aggregate([
  {
    $group: {
      _id: { $floor: { $divide: ["$published_year", 10] } },
      count: { $sum: 1 }
    }
  },
  {
    $project: {
      decade: { $concat: [ { $toString: { $multiply: ["$_id", 10] } }, "s" ] },
      count: 1,
      _id: 0
    }
  }
])


// 1. Index on title
db.books.createIndex({ title: 1 })

// 2. Compound index on author and published_year
db.books.createIndex({ author: 1, published_year: -1 })

// 3. Use explain to check performance
db.books.find({ title: "The Alchemist" }).explain("executionStats")
# mongo-assignment
