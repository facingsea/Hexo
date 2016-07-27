---
title: MongoDB初步使用
date: 2016-06-25 14:11:20
description: MongoDB初步使用
categories: 
- mongodb
tags:
- mongodb
---

## 创建数据库
创建数据库是通过use关键字：use [databaseName]，如： `use foobar;`此时并没有真正的创建foobar这个数据库，而是在mongodb的缓存中存在这个数据库，如果此时什么都不做关闭的话，这个空的数据库就会被删除。只有真正的为foobar添加List（模板数据）时，这个数据库才真正的创建。
```bash
> show dbs
local  0.000GB
> use foobar
switched to db foobar
> show dbs
local  0.000GB
```

## 操作数据
使用方式：`db.[documentName].[action]({key:value, key:value…..})`其中，`db`指的是当前创建的数据库`foobar`，`documentName`是要操作的文档（类似于关系型数据库的表），这个文档可以不存在，没有的话，会自动创建。`action`指的是具体的操作，如`insert`。（）括号里的数据是bson数据，类似于json格式，如命令：`db.persons.insert({name:”zhangsan”, sex:”male”})`
```bash
> db.persons.insert({name:"zhangsan", sex:"male"})
WriteResult({ "nInserted" : 1 })
> show dbs
foobar  0.000GB
local   0.000GB
```

## 查看文档
命令： `show collections`
```bash
> show collections
persons
```

## 查询文档数据
查询所有： `db.[tableName].find()`
查询第一条数据： `db.[tableName].findOne()`
```bash
> db.persons.find()
{ "_id" : ObjectId("576e2b94fad07874af073963"), "name" : "zhangsan", "sex" : "male" }
> db.persons.findOne()
{
        "_id" : ObjectId("576e2b94fad07874af073963"),
        "name" : "zhangsan",
        "sex" : "male"
}

```

## 更新文档内容
命令： `db.[tableName].update({key1: value1}, { $set:{key2:value2}})`
Key1和value2表示查询的条件，$set表示要设置的值是key2/value2
```bash
> db.persons.update({name:"zhangsan"}, {$set:{sex:"ismale"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.persons.find()
{ "_id" : ObjectId("576e2b94fad07874af073963"), "name" : "zhangsan", "sex" : "ismale" }
{ "_id" : ObjectId("576e2d33fad07874af073964"), "name" : "lisi", "sex" : "female" }
```
另一种方式:
```bash
> var p = db.persons.findOne()
> db.persons.update(p, {name: "zhangsan01"})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.persons.find()
{ "_id" : ObjectId("576e2b94fad07874af073963"), "name" : "zhangsan01" }
{ "_id" : ObjectId("576e2d33fad07874af073964"), "name" : "lisi", "sex" : "female" }
> p
{
        "_id" : ObjectId("576e2b94fad07874af073963"),
        "name" : "zhangsan",
        "sex" : "ismale"
}
```
但是这种方式在修改时会只针对update后的语句进行更新，而舍弃其他字段
> 注意：如果查询条件有多个符合的，那么mongodb默认只修改符合的第一条

## 删除文档数据
命令： `db.[tableName].remove({………})`
```bash
> db.persons.find()
{ "_id" : ObjectId("576e2b94fad07874af073963"), "name" : "zhangsan01" }
{ "_id" : ObjectId("576e2d33fad07874af073964"), "name" : "lisi", "sex" : "female" }
> db.persons.remove({name: "lisi"})
WriteResult({ "nRemoved" : 1 })
> db.persons.find()
{ "_id" : ObjectId("576e2b94fad07874af073963"), "name" : "zhangsan01" }
```

## 删除文档集合
命令： `db.[documentName].drop()`
```bash
> show collections
persons
> db.persons.drop()
true
> show collections
>
```

## 删除数据库
命令： `db.dropDatabase()`
```bash
> show dbs
foobar  0.000GB
local   0.000GB
> db.dropDatabase()
{ "dropped" : "foobar", "ok" : 1 }
> show dbs
local  0.000GB
```

## shell中的help
里面所有的shell可以完成的命令帮助，全局的`help`，数据库相关的是`db.help()`，集合相关的是`db.[documentName].help()`
help：
```bash
> help
        db.help()                    help on db methods
        db.mycoll.help()             help on collection methods
        sh.help()                    sharding helpers
        rs.help()                    replica set helpers
        help admin                   administrative help
        help connect                 connecting to a db help
        help keys                    key shortcuts
        help misc                    misc things to know
        help mr                      mapreduce

        show dbs                     show database names
        show collections             show collections in current database
        show users                   show users in current database
        show profile                 show most recent system.profile entries with time >= 1ms
        show logs                    show the accessible logger names
        show log [name]              prints out the last segment of log in memory, 'global' is default
        use <db_name>                set current database
        db.foo.find()                list objects in collection foo
        db.foo.find( { a : 1 } )     list objects in foo where a == 1
        it                           result of the last line evaluated; use to further iterate
        DBQuery.shellBatchSize = x   set default number of items to display on shell
        exit                         quit the mongo shell
```
db.help()：
```bash
> db.help()
DB methods:
        db.adminCommand(nameOrDocument) - switches to 'admin' db, and runs command [ just calls db.runCommand(...) ]
        db.auth(username, password)
        db.cloneDatabase(fromhost)
        db.commandHelp(name) returns the help for the command
        db.copyDatabase(fromdb, todb, fromhost)
        db.createCollection(name, { size : ..., capped : ..., max : ... } )
        db.createUser(userDocument)
        db.currentOp() displays currently executing operations in the db
        db.dropDatabase()
        db.eval() - deprecated
        db.fsyncLock() flush data to disk and lock server for backups
        db.fsyncUnlock() unlocks server following a db.fsyncLock()
        db.getCollection(cname) same as db['cname'] or db.cname
        db.getCollectionInfos([filter]) - returns a list that contains the names and options of the db's collections
        db.getCollectionNames()
        db.getLastError() - just returns the err msg string
        db.getLastErrorObj() - return full status object
        db.getLogComponents()
        db.getMongo() get the server connection object
        db.getMongo().setSlaveOk() allow queries on a replication slave server
        db.getName()
        db.getPrevError()
        db.getProfilingLevel() - deprecated
        db.getProfilingStatus() - returns if profiling is on and slow threshold
        db.getReplicationInfo()
        db.getSiblingDB(name) get the db at the same server as this one
        db.getWriteConcern() - returns the write concern used for any operations on this db, inherited from server object if set
        db.hostInfo() get details about the server's host
        db.isMaster() check replica primary status
        db.killOp(opid) kills the current operation in the db
        db.listCommands() lists all the db commands
        db.loadServerScripts() loads all the scripts in db.system.js
        db.logout()
        db.printCollectionStats()
        db.printReplicationInfo()
        db.printShardingStatus()
        db.printSlaveReplicationInfo()
        db.dropUser(username)
        db.repairDatabase()
        db.resetError()
        db.runCommand(cmdObj) run a database command.  if cmdObj is a string, turns it into { cmdObj : 1 }
        db.serverStatus()
        db.setLogLevel(level,<component>)
        db.setProfilingLevel(level,<slowms>) 0=off 1=slow 2=all
        db.setWriteConcern( <write concern doc> ) - sets the write concern for writes to the db
        db.unsetWriteConcern( <write concern doc> ) - unsets the write concern for writes to the db
        db.setVerboseShell(flag) display extra information in shell output
        db.shutdownServer()
        db.stats()
        db.version() current version of the server
```

集合的help：db.person.help():
```bash
> db.persons.help()
DBCollection help
        db.persons.find().help() - show DBCursor help
        db.persons.bulkWrite( operations, <optional params> ) - bulk execute write operations, optional parameters are: w, wtimeout, j
        db.persons.count( query = {}, <optional params> ) - count the number of documents that matches the query, optional parameters are: limit, skip, hint, maxTimeMS
        db.persons.copyTo(newColl) - duplicates collection by copying all documents to newColl; no indexes are copied.
        db.persons.convertToCapped(maxBytes) - calls {convertToCapped:'persons', size:maxBytes}} command
        db.persons.createIndex(keypattern[,options])
        db.persons.createIndexes([keypatterns], <options>)
        db.persons.dataSize()
        db.persons.deleteOne( filter, <optional params> ) - delete first matching document, optional parameters are: w, wtimeout, j
        db.persons.deleteMany( filter, <optional params> ) - delete all matching documents, optional parameters are: w, wtimeout, j
        db.persons.distinct( key, query, <optional params> ) - e.g. db.persons.distinct( 'x' ), optional parameters are: maxTimeMS
        db.persons.drop() drop the collection
        db.persons.dropIndex(index) - e.g. db.persons.dropIndex( "indexName" ) or db.persons.dropIndex( { "indexKey" : 1 } )
        db.persons.dropIndexes()
        db.persons.ensureIndex(keypattern[,options]) - DEPRECATED, use createIndex() instead
        db.persons.explain().help() - show explain help
        db.persons.reIndex()
        db.persons.find([query],[fields]) - query is an optional query filter. fields is optional set of fields to return.
                                                      e.g. db.persons.find( {x:77} , {name:1, x:1} )
        db.persons.find(...).count()
        db.persons.find(...).limit(n)
        db.persons.find(...).skip(n)
        db.persons.find(...).sort(...)
        db.persons.findOne([query], [fields], [options], [readConcern])
        db.persons.findOneAndDelete( filter, <optional params> ) - delete first matching document, optional parameters are: projection, sort, maxTimeMS
        db.persons.findOneAndReplace( filter, replacement, <optional params> ) - replace first matching document, optional parameters are: projection, sort, maxTimeMS, upsert, returnNewDocument
        db.persons.findOneAndUpdate( filter, update, <optional params> ) - update first matching document, optional parameters are: projection, sort, maxTimeMS, upsert, returnNewDocument
        db.persons.getDB() get DB object associated with collection
        db.persons.getPlanCache() get query plan cache associated with collection
        db.persons.getIndexes()
        db.persons.group( { key : ..., initial: ..., reduce : ...[, cond: ...] } )
        db.persons.insert(obj)
        db.persons.insertOne( obj, <optional params> ) - insert a document, optional parameters are: w, wtimeout, j
        db.persons.insertMany( [objects], <optional params> ) - insert multiple documents, optional parameters are: w, wtimeout, j
        db.persons.mapReduce( mapFunction , reduceFunction , <optional params> )
        db.persons.aggregate( [pipeline], <optional params> ) - performs an aggregation on a collection; returns a cursor
        db.persons.remove(query)
        db.persons.replaceOne( filter, replacement, <optional params> ) - replace the first matching document, optional parameters are: upsert, w, wtimeout, j
        db.persons.renameCollection( newName , <dropTarget> ) renames the collection.
        db.persons.runCommand( name , <options> ) runs a db command with the given name where the first param is the collection name
        db.persons.save(obj)
        db.persons.stats({scale: N, indexDetails: true/false, indexDetailsKey: <index key>, indexDetailsName: <index name>})
        db.persons.storageSize() - includes free space allocated to this collection
        db.persons.totalIndexSize() - size in bytes of all the indexes
        db.persons.totalSize() - storage allocated for all data and indexes
        db.persons.update( query, object[, upsert_bool, multi_bool] ) - instead of two flags, you can pass an object with fields: upsert, multi
        db.persons.updateOne( filter, update, <optional params> ) - update the first matching document, optional parameters are: upsert, w, wtimeout, j
        db.persons.updateMany( filter, update, <optional params> ) - update all matching documents, optional parameters are: upsert, w, wtimeout, j
        db.persons.validate( <full> ) - SLOW
        db.persons.getShardVersion() - only for use with sharding
        db.persons.getShardDistribution() - prints statistics about data distribution in the cluster
        db.persons.getSplitKeysForChunks( <maxChunkSize> ) - calculates split points over all chunks and returns splitter function
        db.persons.getWriteConcern() - returns the write concern used for any operations on this collection, inherited from server/db if set
        db.persons.setWriteConcern( <write concern doc> ) - sets the write concern for writes to the collection
        db.persons.unsetWriteConcern( <write concern doc> ) - unsets the write concern for writes to the collection
```
以上是对集合的具体操作，如：db.person.count()
```bash
> db.persons.count()
0
```
