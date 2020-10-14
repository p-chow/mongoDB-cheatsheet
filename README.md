# mongoDB-cheatsheet

## query document
##### non-structured: 
`db.colName.find(query, projection)`

##### structured: 'db.colName.find().pretty()` 
   `pretty()` : returns document name and value pair displayed
   
   `limit()` : limit by given number from current position
   
   `skip()` : skip the given number of documents
   
   `sort({<key>:1})` : 1 for ascending, -1 for descending
   
##### projection: 
`find({},{<key>:1, _id:0})`
   1 to display field, _id field is always displayed (0 to hide field)


## query operator `db.col.find()`
`{ <field> : {<operator1> : <value 1> },...}`
eg. db.inventory.find({ satus : {$in : ["A","D"]}})

##### equality
`{<key>:<value>}`

##### less than
`{<key>:{$lt:<value>}}`

##### less than equals, greater than, greater than equals, not equals
replace `$lt` with `$lte` or `$gt` or `$gte` or `$ne`

##### other operators
`$in` :

`$and` : .find( {$and: [{"key1":"value1"}, {"key1":"value1"}] }]
=> .find( {"key1":"value1", "key2":"value2"} )
> similar to: "WHERE key1 = value1 AND key2 = value2"

## aggregate functions `db.col.aggregate()` WHERE 
aggregation operations group values from multiple documents together, and performa variety of operations on grouped data to return a single result
`db.colName.aggregate(<aggregate operation>)`

`$sum()` : 
```
     aggregate(
             [{$group: 
                {_id:"$by_user", num_book:{$sum : "$likes"}
                   }}])
```      
> similar to recursive, group documents by the field "by_user", then for every row where id = "by_user", add number of likes of the row to the corresponding value num_book
used similarly: `$avg`, `$min`, `$max`

`$or` + `$match`: 
```
      aggregate(
             [ {$match :
                 {$or :
                   {<query operator>} , {<query operator>} } } },  
               {$group :
                   { _id: null, count: { $sum: 1 } } } ])
```
`$group` :
```
      aggregate (
             [ {$group :
                  {_id: <expression to be grouped by, eg. $by_user>,
                     <field> : {<accumulator> : <expression>},...} } ] )                
```
`$avg` `$sum`
```
      aggregate(
            [ {$group:
                { _id : null,
                 totalPrice: { $sum: { $multiply: [ "$price", "$quantity" ] } },
                 averageQuantity: { $avg: "$quantity" }, 
                 count: { $sum: 1 } } } ] )
```
`$elemMatch` :
```
      find( 
        {<field> : { $elemMatch:{ <key> : <expression> , <key> : <expression> }}});
```
to match both criteria, if not
```
      find( 
        {<field.key> : {<expression>,...}});
```
 
 db.students.aggregate([{ $match: { "scores.0.score": {$gt:80}}},{ $count: "Passed exam with greater than 80 marks"}]);
