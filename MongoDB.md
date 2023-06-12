# MongoDB

- General purpose document databse
- stores data in document , similar to json file
- used in E commerce, iot , gaming , mobile apps etc....
- Advantages
  - Scalability
  - privacy & security
  - resilience
  - speed of development'
- Document is basic unit of data
- Collection is grouping of document
- Databse is cotainer of collection

## Document

- Data is stored in document
- Displayed as JSON format, but stored in BSON(binary JSON) format
- BSON provides additonal datatypes compared to JSON
- Datatypes supported by BSON
  - String
  - Array
  - Object
  - Boolean
  - Null
  - Dates
  - Numbers
  - ObjectID
- Every doc must have `_id` which is a primary key of type ObjectID
- Flexible schema

Syntax:

```
{
	"key":value,
	"key":value,
	"key":value
}
```

Example :

```
{
	"_id": 1,
	"name": "AC3 Phone",
	"colors" : ["black", "silver"],
	"price" : 200,
	"available" : true
}
```

## Connection String components

`mongodb://username:password@host[:port]?<options>`

## Commands

- login to atlas accoung

  ```
  atlas auth  login
  ```

- list all the databases :

  ```
  show dbs
  ```

- create and/or use database :

  ```
  use databaseName
  ```

- check mongodb status

  ```
  db.hello()
  ```

- create a new collection :

  ```
  db.createCollection('collectionName')
  ```

- select the collection

  ```
  db.<collection>
  ```

## CRUD Operations

### Insert

- insert one document to the collection :

  ```
  db.<collection>.insertOne
    { key1: "value1", key2: "value2" }

  ```

  Example : `db.accounts.insertOne({name: "John Doe",age: 30,})`

- insert multiple documents to the collection

  ```
  db.<collection>.insertMany([
    { key1: "value1", key2: "value2" },
    { key1: "value3", key2: "value4" },
    { key1: "value5", key2: "value6" }
  ])
  ```

  Example :

  ```
  db.accounts.insertOne([
    {name: "John Doe",age: 30,},
    {name: "Albert Einstein",age: 80,}
  ])
  ```

### Find

- find documents

  ```
  db.<collection>.find({
    <field> : <value>
    })
  ```

  Example :

  ```
  db.sales.find(
    { id: ObjectId('5bd761dcae323e45a93ccff4') }
  )
  ```

- use `$eq` to select document with specified value (alternative to above query)

  ```
  db.<collection>.find({
    <field> : {
      $eq : <value>
      }
    })
  ```

  Example :

  ```
  db.sales.find(
    { _id:
      { $eq: ObjectId('5bd761dcae323e45a93ccff4') }
    }
  )
  ```

- Use `$in` to select documents with values in the array

  ```
  db.<collection>.find({
    <filed>:{
      $in:[<value>,<value>,....]
    }
  })
  ```

  Example :

  ```
  db.sales.find(
      { storeLocation :
        { $in: ["London", "New York"] }
      }
  )
  ```

#### Comparison operators

These can be used with find commands

- `$gt` greater than
- `$lt` less than
- `$lte` less than or equal to
- `$gte` greater than or equal to

Example :

```
db.sales.find(
    { "customer.age" :
      { $gt: 50 }
    }
  )
```

#### Querying Arrays

- Find method will not only looks for scalar values, but it can also lookthrough arrays for matching values
- `$elemMatch` will find all document that contains the sub document
- kind of subquery in sql

  ```
  db.<collection>.find({
    <field> : {
      $elemMatch :{
        <query>
      }
    }
  })
  ```

  Example :

  ```
  db.accounts.find(
    { "produce" :
      { $elemMatch :
        { $eq : "InvestmentStock" }
      }
    }
  )
  ```

#### Logical Operators

- `$and` : returns all the documents satisfying the expressions

  ```
  db.<collection>.find({
    $and:[
      { <expression> },
      { <expression> },
      ....
    ]
  })
  ```

  Example :

  ```
  db.sales.find(
    { $and : [
      { purchaseMethod : "Online" },
      { couponUsed : true },
      { "customer.age" :
        { $lte : 25 }
      }
    ] }
  )
  ```

  it has an implicit syntax : ` db.collection.find( {<expresiion>, <expresiion>} )` , here `,` acts as `$and`

- `$or` : returns the document that satisfies atleast one of the expression

  ```
  db.<collection>.find({
    $or:[
      {<expression>},
      {<expression>},
      ....
    ]
  })
  ```

  Example :

  ```
  db.sales.find(
    { $or : [
      { "items.name" : "pens" },
      { "items.tags" : "writing" }
    ] }
  )
  ```

- We can use these logical operators together also.

### Replace

- Replace a single document with `replaceOne()`

  ```
  db.<collection>.replaceOne(
    <filter>, <replacement>, {options}
  )
  ```

  Example :

  ```
  db.birds.replaceOne(
    { _id : ObjectId("6286809e2f3fa87b7d86dccd") },
    { "common_name" : "Morning Dove", "scientific_name": "Zenaida macroura" }
  )
  ```

### Update

- update a single document
  ```
  db.<collection>.updateOne(
    <filter>, <update>, {options}
  )
  ```
- `$set` : used to add new fields or replace the existing value of a field
  Example :
  ```
  db.podcasts.updateOne(
    { _id : ObjectId("6286809e2f3fa87b7d86dccd") },
    { $set : { subscriber : 98645 } }
  )
  ```
- `$push` : appends a value to an array or, adds the array field with the value as its element if array is not present
  Example :
  ```
  db.podcasts.updateOne(
    { _id : ObjectId("6286809e2f3fa87b7d86dccd") },
    { $push : { hosts : "Nick rooby" } }
  )
  ```
- upsert (**UP**date or in**SERT**):(option) inserts a document with provided information if matching documents don't exist
  Example :
  ```
  db.podcasts.updateOne(
    { title : "Im inevitable" },
    { $set : { topics : "action" } },
    { upsert : true }
  )
  ```

### updateMany

```
db.<collection>.updateOne(
    <filter>, <update>, {options}
  )

```

Example

```
db.books.updateMany(
  { year : 2023 },
  { $set : { instock : true }}
)
```

### findAndModify

```

db.<collection>.findAndModify({
query: <filter>,
update: <update>,
new: <boolean value>
})

```

- `new` set to true will return the updated document
  Example :

```

db.birds.findAndModify({
query: { common_name: "Blue Jay"},
update: { $inc : { sightings_count:1 } },
new :true,
})

```

### Delete

- Delete one document
  ```
  db.collection.delete(
    <filter>, {options}
  )
  ```
  Example :
  ```
  db.birds.deleteOne({
    _id: ObjectId("62cddf53c1d62bc45439bebf")
  })
  ```
- Delete multiple documents
  ```
  db.collection.deleteMany(
    <filter>, {options}
  )
  ```
  Example :
  ```
  db.birds.deleteMany({
  sightings_count:{
    $lt : 10
  }
  })
  ```

## Sorting & limiting

- **Cursor** : Pointer to the result set of a query
- `find()` method returns a cursor
- Cursors can be used to perform actions on result set

### sort

```
db.collection.find(<query>).sort(<sort>)
```

Example :

```
db.companies.find(
  {category_code:"music"}
)
.sort(
  {name:1}
)
```

- here the result set returned by the `find()` method is sorted by `name` attribute in ascending order.
- `1` specifies ascending order, `0` specifies descending order

### limit

- limit the result set to specified number of documents

  ```
  db.collection.find(<query>).limit(<number>)
  ```

## Projection

- selecting a subset of fileds or columns

```
db.collection.find(
   <query>, <projection>
)
```

- to include a filed use `1` and to exclude a field use `0`
- below example includes the field1 and excludes field2
  `db.collection.find( <query>, {<field1>:1,<field2>:0})`

  Example :

  ```
  db.sales.find(
    {storeLocation:"Denver"},
    {saleDate:1,storeLocation:1,purchaseMethod:1, _id:1})
  ```

## count

- to count no of documents

  ```
   db.collection.countDocuments(
    <query>,
    <options>
   )

  ```

  Example :

  ```
  db.sales.countDocuments(
    {couponUsed:true, storeLocation:"Denver"}
  )
  ```
