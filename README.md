### Section 10

## Intro to ExpressJS

    Express JS is a web framework for NodeJS
    Nest JS is also a web framework.

    Install Command : npm i express

    It will downlaod all the dependencies of it.

    Eg. server
    import express from "express";
    const app = express();
    app.get("/", (req, res) => {
        res.send("Hello World");
    });
    app.listen(4000);

    // res.send() also set the content-type header as HTML
    // res.end() only send plain text

    We can disable headers in express:
        app.disable(HeaderName);

    Behind the Scenes ExpressJS use http server to handle all methods and start the server.

## MiddleWare

    A middleware is a handler function that can process incoming requests and outgoing responses in an Express app.

    (req, res, next) => { ... }
    You can have multiple middlewares chained together.
    Each middleware must call next() to pass control to the next one.

    Example with Multiple Middlewares
        app.get("/",
            (req, res, next) => {
                console.log("M1");
                next(); // moves to next middleware
            },
            (req, res) => {
                console.log("M2");
                res.send("Done");
            }
        );
        If next() is not called, the next middleware is never executed.

    Behind the Scenes

        Express stores all middlewares for a route in an array.
        When a request comes in, it executes them in order.
        If the last middleware calls next(), nothing happens (no more middleware left).

    Types of Middleware
    1. Request Handler Middleware

        (req, res, next) => { ... }
        Runs on every request.
        Used for logging, parsing, authentication, etc.

    2. Error Handling Middleware

        (err, req, res, next) => {
            console.error(err);
            res.status(500).send("Something went wrong!");
        }
        Must have 4 parameters.
        Triggers when:
            A previous middleware calls next(err)
            An error is thrown in a previous middleware

    Built-in Error Handling

        Express includes default error handling.
        But you can override it with a custom error handler like this:
        app.use((err, req, res, next) => {
            res.status(500).send(`Custom error: ${err.message}`);
        });

## HTTP Methods in Express

    In Express, each HTTP method has a corresponding function:

    app.get(path, handler);     // Read data
    app.post(path, handler);    // Create data
    app.put(path, handler);     // Replace data
    app.patch(path, handler);   // Update partial data
    app.delete(path, handler);  // Delete data
    app.all(path, handler);     // Handle all methods

## Difference between Route and Request URL

    Route: Path pattern in your code (`/user/:id`)
    Request URL: Actual URL from client (`/user/42?sort=name`)

    Route is what you define.
    Request URL is what client sends.

## Global Middleware

    A global middleware is a function that runs for every request coming into your Express app — regardless of the route or HTTP method.

    Syntax:
    app.use(middlewareFunction);

    app.use() is used to register middleware globally.
    It should be placed before all route handlers, so it runs before any route is matched.

    Important Notes

        Global middleware must be above all route definitions.
        If next() is not called, the request will hang and never reach the route.
        You can chain multiple app.use() for different purposes.

## Route Specific Middleware

    Use: app.use('/path', middleware)
    Runs for all routes starting with /path

    Example:

    app.use('/user', (req, res, next) => {
    console.log("Middleware for /user");
    next();
    });

    ✅ Runs for: /user, /user/123
    ❌ Doesn't run for: /admin

## Serving Static Files in Express

    Step 1: Put files in a folder (e.g., public)
    Step 2: Use express.static() middleware

    app.use(express.static('public'));
    Serves files like images, CSS, JS directly from /public.
    Example:
    http://localhost:3000/logo.png → serves public/logo.png

    Send File Directly:
    res.sendFile(\_\_dirname + '/path/to/file.txt');
    Sends a specific file using absolute path.

## Sending JSON Data

    we can use res.json() it setheader of application/json
    we can also set statusCode:
    res.status(code).json();

## CORS

    CORS (Cross-Origin Resource Sharing) is a security feature implemented by web browsers that allows or restricts web pages from making requests to a different origin (domain, protocol, or port) than the one that served the web page.
    Introduced: Around 2006 as a standard by the W3C to overcome the limitations of the Same-Origin Policy.

    Why CORS is Needed?

    By default, the browser’s Same-Origin Policy blocks requests made from one origin to another for security reasons (to prevent Cross-Site Request Forgery - CSRF or Cross-Site Scripting - XSS attacks).
    So, CORS provides a controlled way to relax that policy.

    What is an Origin?

        An origin is defined by three things:
            Protocol (http or https)
            Domain (example.com)
            Port (:3000, :5000, etc.)
            If any of these change, it becomes a different origin.

        Example:
            Frontend: http://localhost:3000
            Backend: http://localhost:5000

        A fetch request from frontend to backend is a cross-origin request, and the browser will block it unless the backend allows it using CORS headers.


    How to Enable CORS (in Express)

        Install CORS middleware: npm install cors

        Then in your server code:
        const cors = require('cors');
        app.use(cors());
            OR
        restrict to a specific origin:
        app.use(cors({
        origin: 'http://localhost:3000'
        }));

    Behind the Scenes: CORS Headers

        When the server receives a cross-origin request, it responds with special headers like:

        Access-Control-Allow-Origin: http://localhost:3000
        Access-Control-Allow-Methods: GET, POST
        Access-Control-Allow-Headers: Content-Type

## PreFlight and Options request

    For non-simple requests (e.g., with custom headers or methods like PUT, DELETE), the browser first sends an OPTIONS request called a preflight to check if the server allows it.

    If the server responds with proper headers, only then the actual request is made.

    Simple Request
    GET, POST (with basic content types)
    No preflight

    Complex Request
    PUT, DELETE, PATCH, or with custom headers
    Preflight (OPTIONS) sent

## Types of ROuting

    Dynamic Routing
        Use : to define route parameters.
        Example: /users/:id → req.params.id

    Optional Routing
        Add ? to make params optional.
        Example: /books/:id? matches /books and /books/123

    Wildcard Routing
        Use {*any} to match any trailing path.
        Example: /files/{*any }or /files/path/to/file
        usefull for nested folders/files

    Regex Routing
        Use regex to match routes precisely.
        Example: /^\/user\/(\d+)$/

## Path Traversal Vulnerability

    Path Traversal Vulnerability happens when an attacker tricks your app into accessing files outside allowed folders by using special path parts like ../. This can let them read sensitive files they shouldn’t see.

## Path Module

    path.join()
        Joins multiple path segments into one path and normalizes it (removes .., extra slashes).
        Example: path.join('/foo', 'bar', 'baz') → /foo/bar/baz

    path.normalize()
        Normalizes a path by resolving .., . and redundant slashes without joining.
        Example: path.normalize('/foo/bar//baz/../qux') → /foo/bar/qux

    path.resolve()
        Resolves a sequence of paths into an absolute path. Starts from rightmost path, prepends current dir if needed.
        Example: path.resolve('foo', '/bar', 'baz') → /bar/baz

    path.basename()
        Returns the last part (filename) of a path.
        Example: path.basename('/foo/bar/baz.txt') → baz.txt

    path.dirname()
        Returns the directory part of a path (everything except the last segment).
        Example: path.dirname('/foo/bar/baz.txt') → /foo/bar

## Express Router

    express.router() return an router object

## Rest API Basics

    Introduction

    A REST API (Representational State Transfer) is a set of rules and conventions for building web services that interact over HTTP. RESTful APIs are widely used due to their simplicity, scalability, and reliance on standard web protocols.

    Key Features of an API

        Abstraction: It hides internal details of how things work, exposing only what the client needs.

        Interoperability: APIs work across different platforms and programming languages, enabling seamless integration.

        Automation: REST APIs allow systems to automate tasks by communicating programmatically.

        Scalability: Statelessness and standardized architecture help APIs scale efficiently.

    Introduction to REST API

    REST is an architecture, not a protocol. It treats everything as a resource, which can be accessed through URLs using HTTP methods like GET, POST, PUT, and DELETE. REST APIs provide a flexible, lightweight way to exchange data—typically in JSON format—between clients and servers.

    Key Principles of REST API

        Resource-Based: Each entity (like a user, post, or product) is treated as a resource and accessed via a unique URI.

        Stateless: Every request from a client must contain all the information needed to process it. The server doesn’t store session data.

        HTTP Methods: REST uses standard methods—GET (read), POST (create), PUT/PATCH (update), DELETE (remove).

        Representation of Resources: Data is usually exchanged in JSON or XML format, depending on the request headers.

        Client-Server Architecture: The client and server are separate entities. The client handles UI/UX, while the server handles logic and data.

    Best Practices

        Use descriptive URLs that clearly represent the resource (e.g., /users/123 instead of /getUser?id=123).
        Always follow HTTP standards and return appropriate status codes and messages.
        Version your API by including the version number in the URL (e.g., /api/v1).
        Provide clear error messages along with proper status codes so clients know what went wrong.
        Ensure secure authentication and authorization, such as using tokens or OAuth.
        Paginate large datasets to avoid sending too much data at once.
        Implement rate limiting to protect your API from abuse or excessive use.
        Use caching wherever possible to improve performance and reduce server load.

##  Error Handling in Express

    In Express, a global error handler is a special middleware that catches all errors in the app. It has four parameters:  (err, req, res, next)

    You define it at the end of all routes:

    app.use((err, req, res, next) => {
    res.status(err.status || 500).json({
        message: err.message || "Something went wrong!",
    });
    });

    You trigger it by throwing an error or calling next(err) in any route.
    Useful for showing proper error messages and avoiding server crashes.

## Multer For file Upload

    Multer is a Node.js middleware used with Express for handling multipart/form-data, which is primarily used to upload files.

    Basic Setup:
    const express = require('express');
    const multer = require('multer');
    const app = express();

    // Set storage
    const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, 'uploads/'); // Folder to store files
    },
    filename: (req, file, cb) => {
        cb(null, Date.now() + '-' + file.originalname); // Unique name
    }
    });

    const upload = multer({ storage });

    // Route to handle file upload
    app.post('/upload', upload.single('myFile'), (req, res) => {
    res.send('File uploaded successfully');
    });

    Note:
        upload.single('myFile'): for one file
        upload.array('files', 5): for multiple files
        Files are stored in the folder you specify (uploads/)

## What are Cookies?

    Cookies are small pieces of data (up to 4KB) stored by the browser as key-value pairs.
    Accessed in JS using document.cookie

    What Do Cookies Store?

        Key, value
        Domain
        Path
        Expiry date
        Size
        Flags like Secure, HttpOnly

    Expiry

        By default, cookies are session-based (deleted when the browser closes).
        You can set custom expiry using:
            max-age (in seconds, preferred)
            expires (specific date)

    Flags

        Secure: Cookie only works on HTTPS
        HttpOnly: Can’t be accessed via JS (adds security)
        SameSite: Controls cross-site requests (protects against CSRF)

    Other Notes

        You can set multiple cookies.
        Special characters must be URL-encoded.
        Third-party cookies are cookies from domains other than the one in the browser’s address bar.

## Server side cookies

    1. How Cookies Are Set from Server

        A cookie is sent from the server using the Set-Cookie header:
        Set-Cookie: name=sahil

        When the browser receives this header in a response, it stores the cookie automatically (if rules match).

    2. HttpOnly Cookies

        Cookies with HttpOnly flag cannot be accessed via JavaScript (document.cookie).
        Only the server can set and read them — great for storing sensitive info (e.g., tokens).

    3. Accessing Cookies in Server

        const cookies = req.headers.cookie;
        However, this gives a raw string. Use cookie-parser to parse:

        const cookieParser = require('cookie-parser');
        app.use(cookieParser());

        console.log(req.cookies); // parsed object

    4. Setting Cookies Using Express

        res.cookie("name", "sahil", {
        maxAge: 1000 * 60 * 60, // 1 hour
        httpOnly: true,
        secure: true,
        sameSite: "strict"
        });

    5. Cross-Origin Cookie Sharing

        When making fetch or XHR calls to another domain:

        Fetch defaults to:
        credentials: "same-origin"

        Values can be:
            "omit" – never send cookies
            "same-origin" – send only if same origin
            "include" – always send (even cross-origin)

        Note: If credentials: "include" is used, the server must respond with:
        Access-Control-Allow-Credentials: true
        Otherwise, the browser will ignore the cookie.

    6. XHR vs Fetch: credentials: true

        In XHR, credentials are set via xhr.withCredentials = true;
        In Fetch, use credentials: "include"

## res.download()

    res.download() in Express sends a file as a download to the client.

    Syntax: res.download(path, filename);

    What is does behind:
        Sets Content-Disposition: attachment header.
        Internally uses res.sendFile() to stream the file.
        Triggers a download in the browser.

    Example:
    res.download("path/to/file.pdf", "report.pdf");

    Browser will download the file as report.pdf

## app.route()

    Used to chain multiple HTTP methods (like GET, POST, etc.) for the same route in a cleaner way.

    Example:
    app.route("/user")
    .get(getUser)
    .post(createUser)
    .put(updateUser);

    Keeps your code organized and readable.
    Functionally same as using app.get(), app.post() separately.
    Use it when methods share the same path.

## router.param() or app.param()

    Runs middleware before any route handler that uses a specific route parameter.
    Used to preprocess or validate that parameter.

    Syntax:
    router.param('paramName', (req, res, next, value) => { ... });

    The middleware gets 4 arguments:
    req, res, next, and the value of the route parameter.

    Useful for loading data or checking parameter validity before reaching the route.

## HTTP Redirection

    Redirect tells the browser to go to a different URL using a status code and a Location header.

    Common status codes:

        300: Multiple choices, client picks from options.
        301: Permanently moved; the browser may change the HTTP method (usually to GET).
        302: Temporarily moved; method may also change.
        303: Always changes method to GET.
        307: Temporary redirect but keeps the same HTTP method.
        308: Permanent redirect, method remains the same.

    How to redirect in Node.js

        res.writeHead(301, { Location: "/folder" });
        res.end();

    How to redirect in Express

        res.redirect('/folder');       // Default 302 redirect
        res.redirect(301, '/folder');  // Explicit 301 redirect

    Express sets the status and Location header automatically.

## Types of Form Data

    1. application/x-www-form-urlencoded (default)

        Fields separated by & (e.g., name=John&age=30)
        File data not sent, only filenames
        Use in Express: express.urlencoded({ extended: false })

    2. multipart/form-data

        Fields separated by boundaries like --WebKitFormBoundary
        File data is sent as binary along with fields
        Used for file uploads
        Handled in Express by Multer middleware

    3. text/plain

        Fields separated by new lines (\n)
        File data not sent, only filenames
        Use in Express: express.text()

    4. application/json

        Data sent as JSON string
        Use in Express: express.json()

## Difference between extended true & false in urlencoded data

    extended: false

        Uses querystring library
        Does not support nested objects
        Parses data into a flat object with string values
        Example:
        { 'user[firstname]': 'John', 'user[lastname]': 'Doe' }

    extended: true

        Uses qs library
        Supports nested objects and arrays
        Parses data into nested objects
        Example:
        { user: { firstname: 'John', lastname: 'Doe' } }

## RegEx in Routes


    Express lets you use regular expressions to match route paths more flexibly.
    Instead of a fixed path string, you use a RegEx pattern to define routes.

    Benefits

        Flexible matching: Match complex or variable patterns that can’t be expressed easily with normal params.
        Fine control: You can restrict allowed values (e.g., digits only).
        Useful for legacy URLs or when paths don’t follow simple patterns.

    Drawbacks

        Harder to read and maintain: RegEx can be complex and confusing for others reading your code.
        No named params: Captured groups are accessed by index (req.params[0]), not by names.
        Can be error-prone if regex is not carefully written.

## Array of multiple routes

    You can handle several routes with the same middleware or handler by passing an array of paths.

    Example:
    app.get(['/dir', '/folder', '/text'], (req, res) => {
    res.send('This works for /dir, /folder, and /text');
    });

    A request to any of /dir, /folder, or /text will trigger this handler.


## Highlights of Express v5

    Check official documentation:
    https://expressjs.com/en/guide/migrating-5
