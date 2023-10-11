# MongoDB

- General purpose document database
- stores data in document , similar to json file
- used in E commerce, iot , gaming , mobile apps etc....
- Advantages
  - Scalability
  - privacy & security
  - resilience
  - speed of development'
- Document is basic unit of data
- Collection is grouping of document
- Database is container of collection

## Document

- Data is stored in document
- Max document size is 16MB
- Displayed as JSON format, but stored in BSON(binary JSON) format
- BSON provides additional datatypes compared to JSON
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

- login to atlas account

  ```
  atlas auth  login
  ```

- list all the databases :

  ```
  show dbs
  ```

- list all collections

  ```
  show collections
  ```

- create and/or use database :

  ```
  use <databaseName>
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
  db.<collection>.insertOne(
    { key1: "value1", key2: "value2" }
  )
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

- Find method will not only looks for scalar values, but it can also look through arrays for matching values
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

  it has an implicit syntax : ` db.collection.find( {<expression>, <expression>} )` , here `,` acts as `$and`

- `$or` : returns the document that satisfies at least one of the expression

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

- selecting a subset of fields or columns

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

## Aggregation

- **Aggregation** is analysis and summary of data
- **Stage** is aggregate operation performed on the data
- **Aggregation pipeline** is series of od stages completed one at a time, in order

```
db.collection.aggregate([
  { $stage_name: {<expression >} },
  { $stage_name: {<expression >} }
])
```

different stages are

- `$match` : filters the data that matches criteria, similar to find

  ```
  {
    $match:<Expression>
  }
  ```

- `$group` : group document based on criteria

  ```
  {
    $group:{
      _id:<expression>,   //group key
      <field>:{<accumalator>:<expression>}
    }
  }
  ```

  - `_id` specifies on which field the aggregate has to be performed
  - `<field>` specifies the name given to aggregate result
  - `<accumalator>` is a stage or aggregate function like `$count`, `$sum` etc
    Example :

  ```
  db.zip.aggregate([
    {
      $match:{state:"CA"}
    },
    {
      $group:{
        _id:"$city,
        totalZip:{$count:{}}
      }
    }
  ])
  ```

- `$sort` : puts the document in a specified order

  - `1` for ascending order, `-1` for descending order

  ```
  {
    $sort: {
        "field_name": 1
    }
  }
  ```

- `$limit` : limits the no. of documents

  ```
  {
    $limit: 5
  }
  ```

  Example :

  ```
  db.zips.aggregate([
    {
      $sort:{
        pop:-1
      }
    }
    {
      $limit:3
    }
  ])
  ```

- `$project` : similar to `find()`, use as the last stage

  ```
  {
    $project:{
      <field>:<value>,
      <field>:<value>,
      .....
    }
  }
  ```

  Example :

  ```
  db.sightings.aggregate([
  {
    $project:{
      date:1,
      species_common:1,
      _id:0
    }
  }])
  ```

  - `<value>` is set `1` to include, and `0` to exclude

- `$set` : add or modify the fields in the pipeline

  ```
  {
    $set:{
      <field>:<value>,
      <field>:<value>,
      .....
    }
  }
  ```

  Example :

  ```
  db.birds.aggregate([
    {
      $set:{
        class: 'bird'
      }
    }
  ])
  ```

- `$count` : count no of documents in the pipeline

  ```
  {
    $count:<field>
  }
  ```

- `$out` : write documents that are returned by an aggregate pipeline into a new collection, it should be a last stage.replaces existing data, or creates new data

  ```
  {
    $out:{
      db: <db>,
      coll:<newCollection>
    }
  }
  ```

  - id `db` is not specified then it replaces the existing collection or db

## Data modeling

> Data that is accessed together should be stored together

- ~ schema

### types of relationships

- **one-to-one**
  ```
  {
    _id:...,
    title:"RRR",
    director:"SS Rajamouli"
  }
  ```
- **one-to-many**
  ```
  {
    _id:...,
    title:"RRR",
    cast:[
      {actor:"Jr. NTR", character:"Bhim"}
      {actor:"Ram Charan Tej", character:"Lead role"}
    ],
    ...
  }
  ```
- **many-to-many**

### Modeling Data Relationship

- **Embedding** :
  - store related data in the same document
  - document inside document
  - single query to retrieve data
  - single operation to update / delete data
  - data duplication
  - large documents
    Example
  ```
  {
    _id:...,
    title:"RRR",
    cast:[
      {actor:"Jr. NTR", character:"Bhim"}
      {actor:"Ram Charan Tej", character:"Lead role"}
    ],
    ...
  }
  ```
- **Referencing** :
  - referencing to document in another collection
  - mostly use `_id` field for referencing
  - no duplication data
  - smaller documents
  - may need to query from multiple documents
    Example
  ```
  {
    _id:...,
    title:"RRR",
    cast:[
      ObjectID("456fner63e343nc3e34),
      ObjectID("456fner63rt24e43334),
    ],
    ...
  }
  ```

### Multi document transaction

- Its performing multiple operations on multiple document or collection within a single transaction.
- Session : used to group db operations that are related to each other & should run together
- Max runtime for transaction is 1min

- Steps in multi document transaction :

  - create a session

    ```
    const session=db.getMongo().startSession()
    ```

  - start session

    ```
    session.startTransaction()
    ```

  - choose database & collection then create account variable
    ```
    const account=session.getDatabase(<database>).getCollection(<collection>)
    ```
  - perform the db operations like `updateOne()`

    ```
    account.updateOne({<filter},{<query>})   // perform on first document
    account.updateOne({<filter},{<query>})   // perform in second document

    ```

  - commit transactions

    ```
    session.commitTransactions()
    ```

  - aborting transaction
    ```
    session.abortTransaction()
    ```

## Index

- special data structure to store small portion of the data
- ordered and easy to search efficiently
- points to document identity
- improves query performance
- default index includes `_id`

- `createIndex()` is used to create an index
- `getIndexes()` is used to list the index
- `explain()` is used to check whether index is being used or not
- `hideIndex(<index>)` can be used to hide the index
- `dropIndex()` is used to delete or drop the index
- `dropIndexes()` is used to delete all index

- Index types
  - Single key : index on one field
  - Compound key : index on more than one field
  - Multi key : index on an array field

### Single key index

- index on single field
- support queries & sort on single field
- create an index

```
db.collection.createIndex({fieldName:1})
```

### Compound key index

- index on multiple field
- can be a multikey index if one of the key contains array field

```
db.collection.createIndex({<fieldName>:1, <fieldName>:1, ...})
```

### Multikey index

- one of the index points to an array field
- only one field can points to an array

```
db.collection.createIndex({<fieldName of array>:1, <fieldName>:1, ...})
```

## Atlas Search

### Search Index

- how a search should be performed

```
{
  $search:{
    "index":<index-name>,
    <operator-name> | <collector-name>:{
      <operator-specification> | <collector-specification>
    },
    "highlight":{
      <highlight-option>
    },
    "count":{
      <count-option>
    },
    "returnStoredSource": true | false
  }
}
```

- **Facets** : buckets that we group our search result into
  - `$searchMeta` allows us to see the facets and how many results are in each buckets

## ACID Transactions

- Group of db operations that will be completed as a unit or not at all
- **ACID Properties**
  - **A**tomicity : all operations either succeed or fail together
  - **C**onsistency : all changes made by operations are consistent with db constraints
  - **I**solation : multiple transactions can happen at same time, without affecting the other
  - **D**urability : changes made by transactions will persist, no matter what
