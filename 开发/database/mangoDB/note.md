# MongoDB

* What is MongoDB
  * Schemaless
    * record1 and record2 do not need to have same fields
  * Use json data formate to store data
    * store in binarized json (Bson)
* key characteristics
  * NoSql database
  * no/ few relations
    * relational data needs to be merged manually
* MongoDB Ecosystem
  * MongoDB Database
    * Self-Managed/ Enterprise
    * Atlas (Cloud)
    * Mobile - MongoDB can be installed on mobile devices
    * Compass
    * BI Connectors
    * MongoDB Charts
  * Stitch
    * Serverless Query API
    * Serverless Functions
    * Database Triggers
* command
  * start service on windows
    * net start MongoDB
  * stop service on windows
    * net stop MongoDB
  * db.\<collection>.find({"\<feature>": "\<value>"})
    * Logic
      * $and - {$and: [{xxx},{yyy}]}
      * $or -  {$or: [{xxx},{yyy}]}
      * $all - {"\<feature>": {$all: [xxx,yyy]} }
      * $in - {"\<feature>": {$in: [xxx,yyy]} }
    * Sort({'\<feature>': 1}) - increading (-1 decreasing)
    * Limit(\<number>)
    * Count()
  * Db.\<collection>.update({\<target>}, {\<changes>}) / db.\<collection>.updateOne(...)
    * $set / $unset - {$(un)set: {xxx}} - can be used in \<changes>
    * $inc - {$inc: {"\<feature>": \<addNum>}}
    * array
      * $push
      * $pull
  * Delete / deleteOne
  
* integrate with nodejs
  
  * ```js
    ```
  
  * 