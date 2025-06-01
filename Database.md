### Section 11

## What is a Database?

    A Database is a structured collection of data that allows efficient storage, access, and management.

    Why Use a Database
        -> Organize and retrieve data efficiently
        -> Ensure data security and integrity
        -> Support multiple users
        -> Handle large data volumes
        -> Provide backup and recovery

    ISAM (Indexed Sequential Access Method)
        Early method of storing data using indexes for faster sequential access.

    DBMS (Database Management System)
        Software that manages databases and handles operations like Create, Read, Update, Delete (CRUD).
        Examples: MySQL, MongoDB, PostgreSQL.

    Components of a Database System
        -> DB Server: Handles client requests and manages data (runs as a TCP server).
        -> DB Client: Connects to the server.
            -> GUI Clients: MySQL Workbench, MongoDB Compass
            -> Terminal Clients: mysql, mongo
            -> Drivers: Used in apps (e.g., MySQL Node.js driver)

        -> Storage Engine: Stores data on disk (e.g., InnoDB, WiredTiger)

    Communication Protocols
        Databases use their own protocols over TCP to talk to clients:
            -> MySQL Protocol
            -> MongoDB Wire Protocol

## Type of Database

    Relational Database (RDBMS)

        -> Stores data in tables, like Excel sheets.
        -> Data is organized in rows and columns.
        -> You must define the structure (schema) before adding data.
        -> Good at handling linked data (e.g., customers and their orders).
        -> Very accurate and reliable – used in banking, business apps.
        -> Needs powerful machines to scale.
        -> Examples: MySQL, PostgreSQL.

    Non-Relational Database (NoSQL)

        -> Stores data in a more flexible way – like JSON files, key-value pairs, or graphs.
        -> You don’t need to define a fixed structure before adding data.
        -> Good for fast-changing or large amounts of data.
        -> Easier to scale across many computers.
        -> Used in apps like social media, real-time chats, analytics.
        -> Examples: MongoDB, Redis, Cassandra.

    In Simple Terms:

        -> Use Relational DB when your data is organized and linked (like school or bank data).
        -> Use Non-Relational DB when your data is messy, big, or changes often (like social media posts or logs).

## Introduction to MongoDB

    MongoDB Shell

## Mongo Shell

    It is a node REPL.
    Since it is a node REPL, we can do all the operations of NodeJS.

    mongo shell uses full node.exe but has modified it for handling database operations.

    Difference between node REPL and mongoSh REPL

    Node REPL:
        1. Undefined is return if there is no return value.
        2. Does not highlights the code.
        3. In case of promise, it returns the full promise object.
        4. We cannot redeclare, redefine a const variable.
        5. await can de used in global space.
        6. Npm can be accessed and used.
        7. Don't have extra commands for handling databases.
        8. process.exit() is used for exiting.

    MongoSH REPL:
        1. Newline character (Empty line) is returned if there is no return value.
        2. Highlights the code.
        3. In case of promise, it returns resolve value of promise, Highlights the Error in case of rejection.
        4. We can redeclare, redefine a const variable.
        5. await cannot be used in global space. ( use async function )
        6. NPM cannot be accessed.
        7. Have extra commands for handling databases.
        8. exit command use to exit the shell

## MongoDB Server Summary

    -> MongoDB Server is mainly built in C++, along with other languages.

    -> Port: It uses a fixed default port 27017 (other ports won't work unless configured).

    -> On installation, MongoDB defaults to a virtual test database, which isn’t created on disk until data is stored.

    -> You can view available databases with:
        show("databases") or its short form show dbs.

## Clients to Connect to MongoDB Server

    -> MongoDB Compass: Official GUI client for interacting with the server.

    -> MongoDB VS Code Extension:
        Acts as another client to connect to the server.

        Often provides more detailed views and features than Compass (like document preview, schema, and queries within the editor).

## Fundamentals of MongoDB

    MongoDB Server by default creates these databases:
    (We should not manipulate them)
        -> admin
        -> config
        -> local

    Database:
        -> The main entity for our app is Database.
        -> It can have multiple collections
        -> Drop database means DELETE Database.
        -> It is only created when we have atleast one collection & one document.

    Collections:
        -> It is a part of Database.
        -> It can have multiple documents.
        -> It is not actually array, but like array.
        -> Drop collection means DELETE Collection.

    Documents:
        -> It is JSON Object, which stores the actual data.

    Understandig this through making storageApp DB:
        Main Entity(DataBase) -> StorageAppDB
        Collection -> directoriesCollection.json
        Document -> Each Directory Object

    Connections:
        -> It means a connection between the client and the server.
        -> There can be different (user) connections on a single server.

    Commands:
        -> use("<Database Name>") OR use <Database Name>
            If the database it not-existing. It create a non-existence database. And use it
        -> show collections
            It will show all the existing collections.

## Create Operation in MongoDB

    Create Operation through GUI:
        -> Click '+' on Connection
        -> Enter DB name, Collection name.
        -> For creating document:
            -> Click on "Add Data" in Collection.
            -> You have 2 Options (import JSON or insert document)
            -> Write your data in JSON Format.

        -> It will store in the main Server.

    Create Operation through Shell:
        -> use <Database Name>
        -> db.<CollectionName>.insertOne(<Document>)
            It will create a collection and insert the data(document) in it.

        -> db.<CollectionName>.insertOne(<Documents>)
            It will create a collection and insert all the data(documents) in it.

## Read Operation in MongoDB

    -> db represent to the current database used.
    -> we can do db.<collectionName>.find()
        It will show all the available documents in it.
        It returns a cursor, it is like an array

    -> For finding specific documents we can pass an object in find()
        db.<collectionName>.find({ user: "23" })

    -> Just like find we also have findOne method,
        Which returns only the first match (as Object not cursor).
        db.<collectionName>.findOne({ user: "23" })

    -> If we want to find document in more depth, use configuration object:
        db.<collectionName>.findOne({ user: {$gt : 2} })

        $gt -> greaterThan
        $gte -> greaterThanEqualTo
        $lt -> LessThan
        $lte -> LessThanEqualTo

## Behinds th Scenes Data Packets

    -> MongoDB Protocol is built on top of TCP.
    -> It make a three way TCP handshake.
    -> Send request-response in every 3-5 Secs for checking it connection is alive or not.
    -> For every DB call(Operation) a request is send to server.
    -> DB calls in Shell is Synchronous

## Update Operation in MongoDB

    -> For updating a document we need to first find it.
    -> db.<Collection>.updateOne(findingObj, {$set: updateObj});

        Eg.
        db.expenses.updateOne({ title: "Grocery" }, {$set: { value: "20" }});

    -> db.<Collection>.replaceOne(findingObj, replaceObj);
        It will replace the whole object

    -> db.<Collection>.updateMany(findingObj, {$set: { value: 240 } });
        It will update all the available finded objects.

## Delete Operation in MongoDB

    Property:
        ->  We cannot delete/Update _id
        -> db.expenses.updateOne({ title: "Grocery" }, {$unset: { value: "" }});
            It will delete the value property from that document

    Document:
        -> db.expenses.deleteOne({ title: "Grocery" });
            It will find the document and delete it

    Collection:
        -> db.<collectionName>.drop()
            It will delete the collection

    Database:
        -> for deleting we should be in the database.
        -> db.dropDatabase()
            It will delete all the collections, document inside the DB.

## DataTypes in MongoDB

    ObjetId
    Number (Int32)
    Number (Int64)
    Double
    Moree...

## ObjectId DataTye

    -> ObjectId is 12 bytes = 24-character hex string.
    -> Structure: 4-byte timestamp, 5-byte machine info, 3-byte counter.
    -> Ensures global uniqueness.
    -> You can override _id, but if not provided, MongoDB generates it automatically.

## MongoDB Number Data Types

    Int32 (Standard Number)

        -> Used for regular integers.
        -> Suitable for most everyday use cases.
        -> Range is from -2,147,483,648 to 2,147,483,647.

    Int64 – NumberLong()

        -> Used for very large integers that exceed the Int32 range.
        -> Required when numbers go beyond JavaScript's safe integer limit.

    Decimal128 – NumberDecimal()

        -> Used for high-precision decimal values.
        -> Ideal for financial or scientific data where rounding errors must be avoided.

## Other Data Types

    String
        -> Most commonly used data type.
        -> Stores text values like "name": "Sahil".

    Boolean
        -> Stores true or false.
        -> Useful for flags or conditions like "isActive": true.

    Date
        -> Stores date and time in ISODate format.
        -> Example: ISODate("2025-05-26T00:00:00Z").

    Array
        -> Stores a list of values.
        -> Example: "tag": ["nodejs", "mongodb"].

    Object (Embedded Document)
        -> Stores nested documents inside a field.
        -> Example: "address": { "city": "Mumbai", "zip": 400001 }.

    null
        -> Stores a null value, like "middleName": null.

    Regular Expression (regex)
        -> Stores a regular expression pattern for matching strings.
        -> Example: { name: { $regex: /^S/ } }.

    MinKey
        -> A special value that is always less than any other value in MongoDB.
        -> Used mainly for internal operations like range queries.

    MaxKey
        -> Opposite of MinKey. Always greater than any other value.
        -> Also used in range operations and comparisons.

## MongoDB Data Storage Summary

    Default data folder on Windows: C:\data\db
    (Data is stored here in binary format.)

    Custom data path:
        -> You can specify your own storage location when starting MongoDB with:
            mongod --dbpath <your_custom_path>

    Important:
        -> The specified folder must exist before running the command.

## MongoDB Config File

    -> It’s a text file where you tell MongoDB how to run.

    -> You can set things like:
        -> Where to save your data (dbPath).
        -> Where to save logs.
        -> What network address and port to use.

    Example to set data folder:
        storage:
        dbPath: C:\my_mongo_data

    -> To start MongoDB using this file, run:
        mongod --config path\to\mongod.config

## We can access dataBase globally by running our server on IPv6

## Running MongoShell Scripts in JS

    ->Run JavaScript files using the Mongo shell:
        mongosh your_script.js

    -> Inside the JS file, write only valid JavaScript code using MongoDB API methods.

    -> Use db.getCollection("name") to access collections, especially if the collection name has special characters or spaces.

## MongoDB Playground

    MongoDB Playground is an interactive environment (mainly in VS Code) that lets you:
        -> Write and run MongoDB queries using JavaScript-like syntax.
        -> Test database scripts like insert, find, update, etc.
        -> Connect to real databases (local or remote).
        -> Preview results inline in the editor.

## MongoDB in Nodejs

    -> MongoDB provides drivers for every popular backend language to work with it.
    -> We also have Driver for NodeJS
    -> Installation: npm i mongodb

    -> Import and Connect
        import { MongoClient } from "mongodb";
        const client = new MongoClient("mongodb://127.0.0.1:27017/");
        await client.connect(); // Connect to MongoDB server

    -> Database Access
        const db = client.db();               // Default 'test' DB
        const db = client.db("DataBaseName"); // Use a specific DB

    -> View Collections
        console.log(await db.listCollections().toArray());
        // Lists all collections in the selected DB

    -> Access Documents in a Collection
        const collection = db.collection("fruits");
        console.log(await collection.find().toArray());
        // Fetches all documents in the 'fruits' collection

    -> List All Databases
        const admin = client.db().admin();
        console.log(await admin.listDatabases());
        // Lists all databases on the server

## MongoDB CRUD Operation in NodeJS

    -> Setup
        const db = client.db("DataBaseName");
        const collection = db.collection("users");

    -> Read
        const users = await collection.find().toArray();

    -> Create
        await collection.insertOne({ name: "XYZ", email: "x@g.com", age: 34 });

    -> Update
        await collection.updateOne(
        { _id: new ObjectId("...") },
        { $set: { age: 35 } }
        );

    -> Delete
        await collection.deleteOne({ _id: new ObjectId("...") });
        await collection.drop();         // Drop collection
        await db.dropDatabase();         // Drop DB

## Cursor in MongoDB

    What is a Cursor?
        -> A Cursor is a JS object returned by .find().
        -> It stores query metadata and doesn't hit DB until a method like .toArray() or .next() is called.

    Cursor as an Async Iterator
        -> const cursor = collection.find(); // returns a cursor
        -> cursor[Symbol.asyncIterator];     // true ⇒ it's iterable

        -> You can use:
            for await (const doc of cursor) {
            console.log(doc);
            }

    Cursor Methods(few)

        -> await cursor.next();     // Returns next document or null
        -> await cursor.hasNext();  // Returns true/false

## Cursor Chaining

    -> Methods like .limit(), .skip(), .sort() return the cursor itself (not a promise).
    -> You can chain them before executing with .toArray() or iterating.
    -> Common Methods
        collection.find()
        .limit(5)                 // Limit result to 5 docs
        .skip(2)                  // Skip first 2 docs
        .sort({ name: 1, age: -1 }) // Sort by name ↑, then age ↓
    -> No DB call is made until you use .toArray(), .next(), or a loop.

## Batch Sizing

    What is Batch Size?
    -> MongoDB returns documents in batches, not all at once.
    -> Batch size controls how many docs are returned per network request.

    Set Batch Size
    -> const cursor = collection.find().batchSize(10);
    -> This sets the batch size to 10 documents.
    -> MongoDB will send results in chunks of 10 until all are returned.

    Notes > Improves memory usage and performance for large datasets.

## Projection

    What is Projection?
    -> Projection controls which fields are returned in the query result.

    Include Fields
    -> // Include only "name" and "email"
    -> const users = await collection.find({}, { projection: { name: 1, email: 1 } }).toArray();

    Exclude Fields
    -> // Exclude "age"
    -> const users = await collection.find({}, { projection: { age: 0 } }).toArray();

    Note
    -> 1 = include, 0 = exclude
    -> You can't mix include and exclude (except for \_id).

## $in Operator

    -> The $in operator in MongoDB checks if a field’s value exists in a specified array.
    -> Syntax:
    { field: { $in: [value1, value2, ...] } }

    -> Example:
    db.users.find({ age: { $in: [18, 25, 30] } });
    Matches users whose age is 18, 25, or 30.

    -> Use case: Efficiently fetch multiple values in one query.

## Why ObjectId is not a String in MongoDB?

    -> ObjectId is a special type in MongoDB that takes only 12 bytes, while a string version would take 24 bytes.
    -> It is more efficient because it uses half the space of a string.
    -> In hex, 2 characters = 1 byte.
    -> An ObjectId is made up of:
        -> Timestamp
        -> Machine ID & Process ID
        -> Counter
    -> MongoDB uses the binary (12-byte) form, not the full string, when inserting or querying.

## Application Architecture (Tiers)

    Tier-1 (Single-Tier)
        -> Runs on one machine, no network.
        -> Eg: Calculator, Notepad

    Tier-2 (Client-Server)
        -> Client: UI
        -> Server: Logic + Database
        -> Eg: MySQL Client, Online banking software

    Tier-3
        -> Frontend: UI
        -> Backend: Logic
        -> Database: Storage
        -> Eg: E-commerce sites, Social media apps

    Tier-N (Multi-Tier)
        -> 3-Tier + Services like CDN, AWS, Auth, Cache
        -> Eg: Netflix, YouTube, Large SaaS apps

## Ordered Inserts

    -> Documents are inserted in order.
    -> If any insert fails, remaining inserts are stopped, and an error is thrown.
    -> To continue inserting remaining valid documents, use:
        { ordered: false }

## Upsert

    -> If the document exists → it gets updated.
    -> If the document doesn’t exist → it gets inserted.
    -> Use the upsert: true flag.
    -> db.collection.updateOne(filter, update, { upsert: true });

## Running Commands in MongoDB

    In Driver we have db.command(), and in shell we have db.runCommand()
    we can run commands instead of using methods for admin task.

## Operators in MongoDB

    There are two types of Operator:
        1. Query & Projection
        2. Update

## Schema

    -> A schema defines the structure of data in a database.
    -> It sets field names, data types, and rules (like required fields).
    -> MongoDB is schema-less, but using schemas (e.g., with Mongoose) ensures data consistency and validation.
    -> Useful for catching errors, maintaining uniformity, and writing reliable queries.

## We can set Validation Schema from MongoDB Compass and also from shell using commands

## jsonschema Validation

    -> Defines strict structure, data types, and rules for documents.
    -> Supports nested validation.
    -> additionalProperties: false blocks extra unwanted fields.

    -> Example with Nested Fields:

    db.createCollection("users", {
    validator: {
        $jsonSchema: {
        bsonType: "object",
        required: ["name", "age", "address"],
        additionalProperties: false,
        properties: {
            name: { bsonType: "string" },
            age: { bsonType: "int", minimum: 18 },
            address: {
            bsonType: "object",
            required: ["city", "zip"],
            additionalProperties: false,
            properties: {
                city: { bsonType: "string" },
                zip: { bsonType: "int" }
            }
            }
        }
        }
    }
    });

## Validation Action Vs Validation Lavel

    -> These two control how MongoDB handles documents that fail validation.

    1) Validation Action
    -> What happens when a document fails validation?
        -> error (default): Reject the insert or update.
        -> warn: Allow the operation but log a warning.
    -> validationAction: "error" // or "warn"

    2) Validation Level
    -> Which documents are checked during validation?
        -> strict (default): Validate all inserts and updates.
        -> moderate: Validate only documents that already have the validated fields.
        -> off (MongoDB 7.0+): Disable validation.
    -> validationLevel: "strict" // or "moderate"

## For finding invalid document in collection

    db.collection("users").find({
    $nor: [{ $jsonSchema: { ...your schema... } }]
    });

## Transactions in Database

    -> In MongoDB, transactions are a feature that allows you to group multiple read and write operations into a single atomic operation — meaning either all operations succeed, or none of them are applied. This is similar to transactions in relational databases (like MySQL or PostgreSQL).

    -> Why Use Transactions in MongoDB?

    -MongoDB is a NoSQL database and was originally designed with single-document atomicity in mind. However, in some cases, you need multi-document or multi-collection atomic operations, such as:
        -> Transferring money between accounts.
        -> Updating multiple collections consistently.
        -> Undoing partial updates if something fails.
        -> For these use cases, MongoDB supports multi-document transactions starting from version 4.0 for replica sets and 4.2+ for sharded clusters.

## Steps to enable transaction in MongoDB

    1. Edit the Config File (mongod.conf)

        -> Add the following under the replication section:
            replication:
                replSetName: rs0

    2. Restart the MongoDB service if installed as a service on Windows

    3. Initiate the Replica Set
        -> In the shell, run: rs.initiate()

    4. Verify Status: rs.status()

    5. Change the connection String: mongodb://localhost:27017/?replicaSet=rs0
    
## ACID Properties

    ACID = Atomicity, Consistency, Isolation, Durability

    -> Atomicity:
    All steps in a transaction happen, or none happen.
    "All or nothing."

    -> Consistency:
    The database follows rules before and after the transaction.
    "Data stays valid."

    -> Isolation:
    Transactions don’t interfere with each other.
    "No mix-ups when multiple users act at once."

    -> Durability:
    Once a transaction is saved, it stays saved — even after a crash.
    "It's permanent."

## Relational Vs Non-Relational DBs

    1. Structure
        -> Relational:
            Stores data in tables (like Excel sheets – rows and columns).
        -> Non-Relational:
            Stores data in flexible formats like JSON, key-value pairs, or graphs.

    2. Schema
        -> Relational:
            Fixed structure – you must define the format before adding data.
        -> Non-Relational:
            No fixed structure – you can store different types of data freely.

    3. Scalability
        -> Relational:
            Grows by getting a bigger machine (vertical scaling).
        -> Non-Relational:
            Grows by adding more machines (horizontal scaling), easier for big systems.

    4. ACID
        -> Relational:
            Follows strict rules to keep data accurate and safe (ACID).
        -> Non-Relational:
            May skip some rules for better speed and flexibility.

    5. Query Language
        -> Relational:
            Uses SQL – a standard language to ask and manage data.
        -> Non-Relational:
            Uses different methods depending on the type (not always SQL).

    6. Examples
        -> Relational:
            MySQL, PostgreSQL, Oracle
        -> Non-Relational:
            MongoDB (document), Redis (key-value), Cassandra (wide-column), Neo4j (graph)

## Embedded Vs Referenced Documents

    Embedded Documents
        -> Meaning: Related data is stored inside the same document.
        -> Example:
            {
            "name": "John",
            "address": { "city": "Mumbai", "zip": "400001" }
            }
        -> Good for: Data used together
        -> Pros: Fast reads
        -> Cons: Can get large, harder to update

    Referenced Documents
        -> Meaning: Related data is stored in a separate document and linked by ID.
        -> Example:
            {
            "name": "John",
            "address_id": "abc123"
            }
        -> Good for: Shared or large data
        -> Pros: Cleaner, flexible
        -> Cons: Requires extra query to fetch related data

## RelationShip Types

    1. One-to-One
        Meaning: One document is related to only one other document.
        Example: A User has one Profile.

    2. One-to-Many
        Meaning: One document relates to many documents.
        Example: A Blog Post has many Comments.

    3. Many-to-Many
        Meaning: Many documents relate to many others.
        Example: Students enrolled in many Courses, and Courses have many Students.

## Install commandline tool for below commands.

## Backup And restore
    mongodump (Backup)
        -> Creates a backup of MongoDB data in BSON format.
        -> Can dump entire DB, specific DB, or collections.
        -> Supports options like compression (--gzip), authentication, and dumping to archive files.
        -> Example:
            mongodump --db mydatabase --out /backup/mongo/

    mongorestore (Restore)
        -> Restores data from BSON backups created by mongodump.
        -> Can restore entire dump, specific DB, or collections.
        -> Supports options like dropping existing data before restore (--drop), authentication, and reading from archives or compressed files.
        -> Example:
            mongorestore --db mydatabase /backup/mongo/mydatabase/

## MongoImport and MongoExport

    mongoexport (Export Data)
        -> Exports data from MongoDB to JSON or CSV files.
        -> Useful for sharing or backing up in readable formats.
        -> Common Options:
            --db, --collection, --out, --type, --fields, --query, --jsonArray
        -> Example:
            mongoexport --db mydatabase --collection users --out users.json

    mongoimport (Import Data)
        -> Imports data into MongoDB from JSON or CSV files.
        -> Used to restore, migrate, or populate data.
        -> Common Options:
            --db, --collection, --file, --type, --fields, --jsonArray, --drop
        -> Example:
            mongoimport --db mydatabase --collection users --file users.json

## When to use what ?
    mongodump / mongorestore (Binary Backup & Restore)
        Use when:
            -> You need a full backup or restore of a database/collection
            -> You want to preserve MongoDB-specific types (e.g. ObjectId, Date).
            -> You’re migrating between MongoDB servers.
            -> You need fast, efficient backup (BSON format).

        Avoid when:
            -> You need human-readable or filtered data.

    mongoexport / mongoimport (Structured Data Transfer)
        Use when:
            -> You want to export/import JSON or CSV.
            -> You need to filter or share specific fields or documents.
            -> You're migrating to another database system (e.g. PostgreSQL).

        Avoid when:
            -> You want a complete backup with indexes and metadata.

## MongoDB Authentication & Authorization
    -> Authentication
        Verifies who the user is.
        Requires username & password.
        Enabled using --auth.
        Uses mechanisms like SCRAM-SHA-256, LDAP, etc.

    -> Authorization
        Controls what the user can do.
        Uses Role-Based Access Control (RBAC).
        Users are assigned roles like read, readWrite, dbAdmin, etc.
     
## Enabling Authentication & Authorization
    -> Start replica set without --auth
        Allows initial setup and user creation.

    -> Initiate the replica set
        Use rs.initiate() with member details.

    -> Create an admin user
        Example: user with root role in the admin database.

    -> Restart all members with --auth
        Enables authentication enforcement.

    -> Connect using credentials
        Use -u, -p, and --authenticationDatabase.

## MongoDB Atlas – Key Points
    What is it?
        -> Cloud-based, fully managed MongoDB service.
        -> Hosted by MongoDB Inc.
        -> Supports AWS, GCP, and Azure.

    Features
        -> No server setup required – fully managed.
        -> Free tier available (M0 cluster)
        -> Built-in security: IP whitelisting, encryption, user roles.
        -> Global clusters for low-latency access.
        -> Auto-scaling & sharding support.
        -> Automated backups with restore options.
        -> Monitoring tools built-in (charts, metrics, alerts).
        -> Easy integrations with Node.js, Python, etc.

    How to Use
        -> Sign up at: mongodb.com/cloud/atlas
        -> Create a cluster (select cloud provider and region).
        -> Add your IP address to access list.
        -> Create a database user with username & password.
        -> Connect using:
            -> MongoDB URI in your app
            -> Compass (GUI)
            -> Mongo Shell

## Self-Managed Databases
    -> You install, configure, and maintain the database yourself.
    -> Responsible for hardware or cloud infrastructure.
    -> Must handle:
        -> Backup & recovery
        -> Security patches and updates
        -> Scaling and performance tuning
        -> High availability and failover
    -> Full control over configuration and environment.
    -> Requires in-house expertise or dedicated DBAs.
    -> Examples: Running MongoDB on your own servers or cloud VMs.

## Managed Databases
    -> Provided as a service by cloud vendors (e.g., MongoDB Atlas, Amazon RDS).
    -> Infrastructure and maintenance handled by the provider.
    -> Features like:
        -> Automated backups
        -> Scaling (vertical & horizontal)
        -> Security and patching managed automatically
        -> Monitoring and alerts included
    -> Easier and faster to set up.
    -> May have some limitations on customization.
    -> Costs typically include the management overhead.
    -> Examples: MongoDB Atlas, AWS RDS, Google Cloud SQL.


