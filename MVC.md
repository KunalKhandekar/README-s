### SECTION 12

## Introduction to MVC Architecture

    MVC (Model-View-Controller) is a design pattern that helps in organizing code by separating concerns in a Node.js application. It divides the application into three main components:

    🔹 Model
        -> Responsible for interacting with the database.
        -> Retrieves, updates, or deletes data.
        -> Represents the business logic.

    🔹 View
        -> Handles the UI (User Interface).
        -> Displays data to the user using server-side rendering.
        -> Note: In modern backend development, especially with REST APIs, the View is often handled on the frontend (e.g., React, Angular), so this layer is usually not used in backend code.

    🔹 Controller
        -> Manages the application logic.
        -> Handles incoming HTTP requests, interacts with Models, and returns the response (usually in JSON).

    Request-Response Flow in MVC
        -> View (UI) sends a request (e.g., via a form or button click).
        -> The Router receives the request and forwards it to the appropriate Controller.
        -> The Controller processes the request, interacts with the Model to fetch or modify data.
        -> The Controller then sends back the appropriate response.

## Controller in MVC
    -> Purpose: Handles incoming HTTP requests, interacts with Models, and sends responses (usually JSON).
    -> Location: Stored in a dedicated controllers/ folder.
    -> Structure: Each controller file contains functions related to a specific resource (e.g., userController.js).
    -> Role in Flow: Router → Controller → Model → Controller → Response.

## View in MVC

    -> The View is responsible for displaying the UI.
    -> In traditional MVC, it handles Server-Side Rendering (SSR) by generating dynamic HTML or JSX.

    Common View Engines in Express:

        EJS (Embedded JavaScript):
            -> Syntax similar to HTML with <%= %> to insert dynamic data.
            -> Easy to integrate.

        Pug (formerly Jade):
            -> Minimalist template engine.
            -> Uses indentation instead of HTML tags.

        Handlebars:
            -> Logic-less templating.
            -> Uses {{ }} for dynamic data.

        react-express-view:
            -> Allows rendering React components (JSX) on the server.
            -> Great for React-based SSR apps.
        
## Models and Mongoose (ODM for MongoDB)

    What is Mongoose?
        -> Mongoose is an ODM (Object Data Modeling) library for MongoDB and Node.js.
        -> It allows you to define schemas and interact with MongoDB using models.

    Connection
        -> mongoose.connect(uri);

    Defining a Model
        -> const Model = mongoose.model("CollectionName", schemaObject);
        -> The CollectionName should be capitalized as a standard practice.
        -> Mongoose will:
            -> Automatically convert the name to lowercase
            -> Pluralize it (e.g., "User" becomes "users")

    Customize Pluralization
        -> mongoose.pluralize((word) => word); // disables pluralization

    Disable Auto Collection Creation
        -> mongoose.set("autoCreate", false);
        -> By default, Mongoose auto-creates collections even if no documents are inserted.
        -> Disabling this avoids unnecessary empty collections.

    Insert Data
        -> const Model = mongoose.model("CollectionName", schemaObject);
        -> await Model.insertOne({ name: "xyz" }); // ⚠️ Use `Model.create()` instead
        -> Note: Model.insertOne() is not a Mongoose method — it's from native MongoDB.
        -> Use:
            await Model.create({ name: "xyz" });

    Schema vs Model
        -> Schema: Defines the shape of documents (application-level).
        -> Model: Provides the interface for interacting with the DB collection using that schema.

## Mongoose Query Behavior

    Connection Dependency
        -> No query is executed until Mongoose is connected to the database.
        -> Queries will be queued internally and executed once the connection is established
        -> mongoose.connect(uri);
        // Only after successful connection, queries will run

    Shared Connection
        -> A single Mongoose connection is reused across all files/modules.
        -> This means you typically connect once (e.g., in index.js or db.js) and then import the models in other files without reconnecting.

        // db.js
        mongoose.connect(uri);

        // userModel.js
        const User = mongoose.model("User", userSchema); // uses the same connection

##  Mongoose Schema Essentials

    -> Common Field Properties
        const userSchema = new mongoose.Schema({
        name: {
            type: String,
            required: [true, "Name is required"],
            minLength: [3, "Name must be at least 3 characters"],
            trim: true,
            lowercase: true,
        },
        age: {
            type: Number,
            min: [3, "Age must be at least 3"],
            required: function () {
            return this.name === "child"; // Conditional required
            },
            default: null
        },
        email: {
            type: String,
            match: [/^[\w-.]+@([\w-]+\.)+[\w-]{2,4}$/, "Invalid email format"],
            uppercase: true
        },
        });

    -> Other Schema Options
        Passed in the second argument of new mongoose.Schema():
        const schemaOptions = {
            strict: true,               // Ignores fields not defined in the schema
            timestamps: true,           // Adds createdAt and updatedAt
            versionKey: false,          // Removes __v field
            collection: "users",        // Custom collection name
            timeseries: {},             // For time-series collections (MongoDB >= 5.0)
        };
        const userSchema = new mongoose.Schema({ /* fields */ }, schemaOptions);

    -> ObjectId Reference
        userId: {
        type: mongoose.Schema.Types.ObjectId,
        ref: "User"
        }
    
## CRUD Operations Using Mongoose

    Setup First (Define Schema & Model)

        const mongoose = require("mongoose");
        const userSchema = new mongoose.Schema({
            name: String,
            email: String,
            age: Number
        });
        const User = mongoose.model("User", userSchema);
        module.exports = User;

    Create Operation

        -> insertOne()
        (Not native to Mongoose — use MongoDB driver or .create() instead)

        -> insertMany()
            Inserts an array of documents:
            await User.insertMany([
                { name: "John", email: "john@g.com" },
                { name: "Jane", email: "jane@g.com" }
            ]);

        -> create()
            Flexible method that handles one or many:

            await User.create({ name: "Mike", email: "mike@g.com" });
            await User.create([{ name: "A" }, { name: "B" }]);

        -> Using Instance & save()
            const user = new User({ name: "Sam", email: "sam@g.com" });
            await user.save(); // Saves to DB

    Read Operation

        -> Find one
            const user = await User.findOne({ email: "xyz@g.com" }).lean();
            Here, .lean() returns plain JS object (better performance, no Mongoose methods)

        -> Find all
            const users = await User.find().lean(); // Array of all users

        -> Find by ID
            const user = await User.findById("user_id").lean();

    Update Operation

        1. Inefficient Way (2 DB Calls)
            const user = await User.findOne({ email: "xyz@g.com" });
            user.age = 12;
            await user.save();

        2. Efficient Way (Single DB Call)
            const updatedUser = await User.findOneAndUpdate(
                { email: "xyz@gg.com" },
                { name: "Shizuka" },
                { new: true, runValidators: true }
            );

            new: true → Returns the updated document
            runValidators: true → Ensures validation is applied during update

    Delete Operation

        -> Delete one by condition
            await User.findOneAndDelete({ email: "xyz@g.com" });

        -> Delete by ID
            await User.findByIdAndDelete("user_id");

        -> Delete many
            await User.deleteMany({ age: { $lt: 18 } });

##  Thenable Objects

    -> A thenable is any object with a .then() method.
    -> It acts like a promise, but isn’t necessarily created using Promise.
    -> Works with await and Promise.resolve().

    -> Example:
        const thenable = {
            then: (resolve, reject) => {
                resolve("Done!");
            }
        };
        await thenable; // Works like a promise

## What is a Mongoose Query?

    A Mongoose query is an object representing a database operation (e.g., find, update, delete). It allows you to build the query before executing it.

    -> Lazy Execution:
        A query doesn't run until you:
            await it
            Call .then()
            Use .exec()

        const query = User.find({ age: { $gt: 18 } }); // not yet executed
        const result = await query; // now it's executed

    -> Chaining:
        await User.find({ age: { $gte: 18 } })
                .select("name email")
                .limit(10)
                .sort({ name: 1 });

##  What is a Mongoose Document?

    -> A document is an instance of a Mongoose model, representing one record in the MongoDB collection.
    -> It is not a plain object — it inherits from Mongoose’s Document class.
    -> Comes with built-in methods, schema validation, and middleware support.

    Key Characteristics
        -> Created using new Model({...}) or retrieved via query.
        -> Can interact with MongoDB using .save(), .deleteOne(), .updateOne(), etc.
        -> Supports validation, middleware, instance methods, and change tracking (.isModified()).

## Custom Validation in Mongoose

    Mongoose allows custom field validation using the validate option in schemas.

    Syntax:
        field: {
            type: String,
            validate: {
                validator: function (val) { return val.length > 3; },
                message: "Field must be longer than 3 characters."
            }
        }

    Alternate Shorthand:
        validate: [validatorFunction, "Error message"]

    Features:
        Return true if valid, false if invalid.
        Use props.value in error messages.
        Supports async validators (e.g., checking uniqueness in DB).

##  ref & populate in Mongoose

    ref: Used in schema to reference another model using ObjectId.
        author: { type: mongoose.Schema.Types.ObjectId, ref: "User" }

    populate(): Replaces the referenced ObjectId with the actual document.
        const post = await Post.find().populate("author");

    Used to create relationships (like foreign keys) between collections.

## Mongoose Virtuals

    -> Virtuals are computed properties in Mongoose documents, not stored in MongoDB.
    -> Common use: derive fullName from firstName + lastName.
    -> Mongoose adds an id virtual by default (string version of _id).

    Creating Virtuals
        -> Getter only
            schema.virtual('fullName').get(() => ...)
        -> Getter + Setter
            schema.virtual('fullName').get(() => ...).set(val => ...)

    Accessing Virtuals
        -> Enable in output
            doc.toJSON({ virtuals: true })
            doc.toObject({ virtuals: true })
        -> Schema-level access: schema.virtuals

    Virtuals & .lean()
        -> By default, virtuals do not work with .lean().
        -> To include them:
            Model.find().lean({ virtuals: true })
    
## Custom Instance Methods

    -> You can define document-level methods using the methods option in the schema:

        const userSchema = new mongoose.Schema({
        name: String
        }, {
            methods: {
                getSummary() {
                return `User: ${this.name}`;
                }
            }
        });

    -> this refers to the document
    -> Used like: user.getSummary()
    -> You can also add them manually:
        userSchema.methods.getSummary = function () { ... }

## Custom Static Methods

    -> You can define model-level methods using the statics option in the schema:

        const userSchema = new mongoose.Schema({
        email: String
        }, {
            statics: {
                async findByEmail(email) {
                return this.findOne({ email });
                }
            }
        });

    -> this refers to the model
    -> Used like: User.findByEmail("abc@example.com")
    -> You can also add them manually:
        userSchema.statics.findByEmail = async function (email) { ... }

## Mongoose Middleware (Hooks)

    Mongoose middlewares (hooks) are functions that run before (pre) or after (post) certain operations like saving, querying, inserting, or aggregating.

    Types of Middleware
        -> Document Middleware (save, validate, remove)
            Used to modify document data (e.g., hash passwords).

        -> Query Middleware (find, findOne, etc.)
            Modify or log queries (e.g., exclude inactive users).

        -> Model Middleware (insertMany)
            Modify documents before/after bulk insert.

        -> Aggregate Middleware (aggregate)
            Edit aggregation pipeline (e.g., exclude deleted docs).

## MongoDB Indexes

    Indexes in MongoDB are special data structures that improve the speed of read operations (like find, sort) by allowing MongoDB to quickly locate documents in a collection, just like an index in a book.

    Why Indexes?
        Without an index, MongoDB performs a collection scan—checks every document. This is slow for large datasets.

    Usage:
        Create: db.collection.createIndex({ field: 1 })
        View: db.collection.getIndexes()
        Delete: db.collection.dropIndex({ field: 1 })

##  unique: true

    Used in schema to enforce unique values for a field.
        -> email: { type: String, unique: true }
        -> It creates a unique index.
        -> If duplicates exist, index creation fails.
        -> Clean duplicates before creating the index.

    Model.init()
        -> Ensures all indexes defined in the schema are created in the DB.
        -> Useful when auto-indexing is off.
        -> await User.init();

    autoIndex in Mongoose
        -> Controls whether Mongoose builds indexes automatically when the app starts.
        -> Schema Level:
            const schema = new Schema({}, { autoIndex: false });
        -> Connection Level:
            mongoose.connect(uri, { autoIndex: false });

## Mongoose Document Versioning

    -> Mongoose uses a __v field to track how many times a document has been modified.
    -> This version key is automatically incremented on .save().
    -> It helps with optimistic concurrency — preventing overwrites if another process updates the same document.
    -> You can enable it with { optimisticConcurrency: true } in the schema.
    -> Changes to arrays or subdocuments also increase __v.
    -> You can customize the version key or disable it using the versionKey option.

## Mongoose Built-in Errors

    CastError
        -> Occurs when a value can't be cast to the required type.
        -> Example:
            await User.findById("invalid-id"); // throws CastError

    ValidationError
        -> Happens when schema validation fails.
        -> Example:
            const user = new User({ email: "" });  
            await user.save(); // throws ValidationError for missing email

    ValidatorError
        -> Thrown for specific field validation failures.
        -> Example:
            email: {
                type: String,
                validate: v => v.includes("@") // if not, throws ValidatorError
            }

    DocumentNotFoundError
        -> Happens when .orFail() is used and no document is found.
        -> Example:
            await User.findById("someid").orFail(); // throws DocumentNotFoundError

    VersionError
        -> Occurs when there's a conflict in the __v version key during save (optimistic concurrency).
        -> Example:
            doc.__v = 2;  
            await doc.save(); // throws VersionError

    OverwriteModelError
        -> Thrown when trying to define a model with an existing name.
        -> Example:
            mongoose.model("User", schema);  
            mongoose.model("User", schema); // throws OverwriteModelError

    MissingSchemaError
        -> Occurs when you try to use a model that hasn’t been defined yet.
        -> Example:
            mongoose.model("Unknown"); // throws MissingSchemaError
