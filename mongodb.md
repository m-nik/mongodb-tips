


# shell
```
mongosh
mongosh "mongodb://172.16.10.52"
mongosh "mongodb://172.16.10.52:27017"
```

# use/create database
In MongoDB, a database is not actually created until it gets content!
```
use mydb
use("mydb")
```

# Create Collection
In MongoDB, a collection is not actually created until it gets content! 
explicitly:
```mongodb
db.createCollection("posts") 
```
implicitly:
```mongodb
db.test2.insertOne({salam:true}) 
```



# insert data
```mongodb
db.posts.insertOne({
  title: "Post Title 1",
  body: "Body of post.",
  category: "News",
  likes: 1,
  tags: ["news", "events"],
  date: Date()
})
```
```mongodb
db.posts.insertMany([  
  {
    title: "Post Title 2",
    body: "Body of post.",
    category: "Event",
    likes: 2,
    tags: ["news", "events"],
    date: Date()
  },
  {
    title: "Post Title 3",
    body: "Body of post.",
    category: "Technology",
    likes: 3,
    tags: ["news", "events"],
    date: Date()
  },
  {
    title: "Post Title 4",
    body: "Body of post.",
    category: "Event",
    likes: 4,
    tags: ["news", "events"],
    date: Date()
  }
])
```


# find
find() accepts a query object. If left empty, all documents will be returned
```mongodb
db.posts.find()
db.test.find( {mjavad: false} )
```
findOne() accepts a query object. If left empty, it will return the first document it finds
```mongodb
db.test.findOne()
```

# projection
find() and findOne() accept an second parameter as projection that describes which fields to include in the results
```mongodb
db.posts.find({}, {title: 1, date: 1})
```

The _id field is also included by default unless specifically excluded.
```mongodb
db.posts.find({}, {_id: 0, title: 1, date: 1})
```

You cannot use both 0 and 1 in the same object. The only exception is the _id field. You should either specify the fields you would like to include or the fields you would like to exclude.
```mongodb
db.posts.find({}, {category: 0})
```

# Update Document
with updateOne() and updateMany()
`$set` changes fields
```mongodb
db.posts.find( { title: "Post Title 1" } ) 
db.posts.updateOne( { title: "Post Title 1" }, { $set: { likes: 2 } } ) 
db.posts.find( { title: "Post Title 1" } ) 
```

insert if not exists with `upsert` option
```mongodb
db.posts.updateOne( 
  { title: "Post Title 5" }, 
  {
    $set: 
      {
        title: "Post Title 5",
        body: "Body of post.",
        category: "Event",
        likes: 5,
        tags: ["news", "events"],
        date: Date()
      }
  }, 
  { upsert: true }
)
```

increment a nummerical field using `$inc`

```mongodb
db.posts.updateMany({}, { $inc: { likes: 1 } })
```


# Delete Documents
deleteOne() and deleteMany()
```mongodb
db.posts.deleteOne({ title: "Post Title 5" })
db.posts.deleteMany({ category: "Technology" })
```

# query operators
**Comparison**
`$eq` Values are equal
`$ne` Values are not equal
`$gt` Value is greater than another value
`$gte` Value is greater than or equal to another value
`$lt` Value is less than another value
`$lte` Value is less than or equal to another value
`$in` Value is matched within an array

```mongodb
db.myData.find({x:{$gt:25}})
db.myData.find({x:{$in:[25,2,7,10]}})
```

**Logical**
`$and` Returns documents where both queries match
`$or` Returns documents where either query matches
`$nor` Returns documents where both queries fail to match
`$not` Returns documents where the query does not match

```mongodb
db.myData.find({$and:[{x:{$in:[25,2,7,10]}}, {y:50}]})
```

**Evaluation**
`$regex` Allows the use of regular expressions when evaluating field values
`$text` Performs a text search
`$where` Uses a JavaScript expression to match documents

# Update Operators
**Fields**
`$currentDate` Sets the field value to the current date
`$inc` Increments the field value
`$rename` Renames the field
`$set` Sets the value of a field
`$unset` Removes the field from the document

**Array**
`$addToSet` Adds distinct elements to an array
`$pop` Removes the first or last element of an array
`$pull` Removes all elements from an array that match the query
`$push` Adds an element to an array



# Aggregation Pipelines
Aggregation operations allow you to group, sort, perform calculations, analyze data, and much more.
Aggregation pipelines can have one or more "stages". The order of these stages are important. Each stage acts upon the results of the previous stage.

```mongodb
db.posts.aggregate([
  // Stage 1: Only find documents that have more than 1 like
  {
    $match: { likes: { $gt: 1 } }
  },
  // Stage 2: Group documents by category and sum each categories likes
  {
    $group: { _id: "$category", totalLikes: { $sum: "$likes" } }
  }
])
```

### Aggregation $group
This aggregation stage groups documents by the unique _id expression provided.
Don't confuse this _id expression with the _id ObjectId provided to each document.
```mongodb
db.listingsAndReviews.aggregate(
    [ { $group : { _id : "$property_type" } } ]
)
```
This will return the distinct values from the property_type field.

### Aggregation $limit
This aggregation stage limits the number of documents passed to the next stage.
```mongodb
db.movies.aggregate([ { $limit: 1 } ])
```

### Aggregation $project
```mongodb
db.restaurants.aggregate([
  {
    $project: {
      "name": 1,
      "cuisine": 1,
      "address": 1
    }
  },
  {
    $limit: 5
  }
])
```


### Aggregation $sort
The sort order can be chosen by using 1 or -1. 1 is ascending and -1 is descending.
```mongodb
db.listingsAndReviews.aggregate([ 
  { 
    $sort: { "accommodates": -1 } 
  },
  {
    $project: {
      "name": 1,
      "accommodates": 1
    }
  },
  {
    $limit: 5
  }
])
```


### Aggregation $match
This aggregation stage behaves like a find. It will filter documents that match the query provided. 
Using $match early in the pipeline can improve performance since it limits the number of documents the next stages must process.
```mongodb
db.listingsAndReviews.aggregate([ 
  { $match : { property_type : "House" } },
  { $limit: 2 },
  { $project: {
    "name": 1,
    "bedrooms": 1,
    "price": 1
  }}
])
```

### Aggregation $addFields
This aggregation stage adds new fields to documents
```mongodb
db.restaurants.aggregate([
  {
    $addFields: {
      avgGrade: { $avg: "$grades.score" }
    }
  },
  {
    $project: {
      "name": 1,
      "avgGrade": 1
    }
  },
  {
    $limit: 5
  }
])
```

### Aggregation $count
This aggregation stage adds new fields to documents
```mongodb
db.restaurants.aggregate([
  {
    $match: { "cuisine": "Chinese" }
  },
  {
    $count: "totalChinese"
  }
])
```

### Aggregation $lookup
This aggregation stage performs a left outer join to a collection in the same database.
There are four required fields:
`from` The collection to use for lookup in the same database
`localField` The field in the primary collection that can be used as a unique identifier in the from collection.
`foreignField` The field in the from collection that can be used as a unique identifier in the primary collection.
`as` The name of the new field that will contain the matching documents from the from collection.
```mongodb
db.comments.aggregate([
  {
    $lookup: {
      from: "movies",
      localField: "movie_id",
      foreignField: "_id",
      as: "movie_details",
    },
  },
  {
    $limit: 1
  }
])
```


### Aggregation out
This aggregation stage writes the returned documents from the aggregation pipeline to a collection. 
```mongodb
db.listingsAndReviews.aggregate([
  {
    $group: {
      _id: "$property_type",
      properties: {
        $push: {
          name: "$name",
          accommodates: "$accommodates",
          price: "$price",
        },
      },
    },
  },
  { $out: "properties_by_type" },
])
```


# Indexing & Search
```mongodb
db.movies.aggregate([
  {
    $search: {
      index: "default", // optional unless you named your index something other than "default"
      text: {
        query: "star wars",
        path: "title"
      },
    },
  },
  {
    $project: {
      title: 1,
      year: 1,
    }
  }
])
```

# Schema Validation
```mongodb
db.createCollection("posts", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: [ "title", "body" ],
      properties: {
        title: {
          bsonType: "string",
          description: "Title of post - Required."
        },
        body: {
          bsonType: "string",
          description: "Body of post - Required."
        },
        category: {
          bsonType: "string",
          description: "Category of post - Optional."
        },
        likes: {
          bsonType: "int",
          description: "Post like count. Must be an integer - Optional."
        },
        tags: {
          bsonType: ["string"],
          description: "Must be an array of strings - Optional."
        },
        date: {
          bsonType: "date",
          description: "Must be a date - Optional."
        }
      }
    }
  }
})

```




# mongodb data API
```mongodb
https://data.mongodb-api.com/app/<Data API App ID>/endpoint/data/v1/action/

https://data.mongodb-api.com/app/<Data API App ID>/endpoint/data/v1/action/findOne
{
  "dataSource": "<data source name>",
  "database": "<database name>",
  "collection": "<collection name>",
  "filter": <query filter>,
  "projection": <projection>
}
```
```mongodb
curl --location --request POST 'https://data.mongodb-api.com/app/<DATA API APP ID>/endpoint/data/v1/action/findOne' \
--header 'Content-Type: application/json' \
--header 'Access-Control-Request-Headers: *' \
--header 'api-key: <DATA API KEY>' \
--data-raw '{
    "dataSource":"<CLUSTER NAME>",
    "database":"sample_mflix",
    "collection":"movies",
    "projection": {"title": 1}
}'
```



# mongodb and NodeJS
```
npm install mongodb
```

```js
const { MongoClient } = require('mongodb');
const uri = "mongodb+srv://<username>:<password>@<cluster.string>.mongodb.net/myFirstDatabase?retryWrites=true&w=majority";
const client = new MongoClient(uri);

async function run() {
  try {
    await client.connect();
    const db = client.db('sample_mflix');
    const collection = db.collection('movies');

    // Find the first document in the collection
    const first = await collection.findOne();
    console.log(first);
  } finally {
    // Close the database connection when finished or an error occurs
    await client.close();
  }
}
run().catch(console.error);

```


# sort and limit and offset
```mongodb
db.myData.find().sort({x:-1}).limit(10);
db.myData.find().sort({x:-1}).limit(10).skip(10)

```


# use JS in mongodb shell
```mongodb
use('login')
for(i=0 ; i<10000; i++) {
    db.myData.insert({x:i, y:2*i})
}
db.myData.count()
```

# drop collection
```mongodb
db.myData.drop();
```






