


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
