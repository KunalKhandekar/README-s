### Section 16

## Redis Summary

    -> Redis (REmote DIctionary Server) is a fast, in-memory key-value store used as a database, cache, and message broker.
    -> It stores data in RAM, making reads/writes very fast.
    -> Supports different data types like strings, lists, hashes, sets, and more.

    -> Common use cases:
        -> Session storage
        -> Caching
        -> Rate limiting
        -> Message queues
        -> Leaderboards

    -> It has features like:
        -> Data persistence
        -> Auto key expiration (TTL)
        -> Pub/Sub messaging
        -> Replication & clustering

    -> Widely used with Node.js and other backend systems for high-speed data tasks.

## Redis - Server, Client & GUI 

    -> redis is not supproted for windows, use WSL for using redis.
    -> Run the following commands to download server & client of redis.
        curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

        echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list

        sudo apt-get update
        sudo apt-get install redis

    -> After installing check the version of redis by: 
        redis-server -v
        redis-cli -v

    -> Default port for redis is 6379
    -> For starting redis as a service:
        sudo service redis-server start

    -> redis commands are not case sensitive
    -> Redis insights GUI tool download

## String Operations

    Key Operations
        -> keys * – Get all keys in the database.
        -> del key – Delete the specified key.

    String Set/Get Operations
        -> set key value – Set a key to hold the string value.
        -> get key – Get the value of a key.
        -> setnx key value – Set the value only if the key does not exist.
        -> getset key value – Get the old value and set the new one.
        -> append key value – Append a value to the existing string.
        -> strlen key – Get the length of the value stored at key.

    String Range
        -> getrange key start end – Get a substring from the stored value.

    Numeric Operations (if value is an integer or float)
        -> incr key – Increment the value by 1.
        -> decr key – Decrement the value by 1.
        -> incrby key number – Increment the value by a specific integer.
        -> decrby key number – Decrement the value by a specific integer.
        -> incrbyfloat key number – Increment the value by a float.

## TTL in redis

    Redis supports setting TTL (Time To Live) for any key. Expiry commands are grouped by when the expiry is applied:

    While Setting Key
        -> SET key value EX seconds – Set with expiry in seconds
        -> SET key value PX milliseconds – Set with expiry in ms
        -> SETEX key seconds value – Legacy string-only alternative to SET ... EX
        -> PSETEX key ms value – Legacy string-only alternative to SET ... PX

    After Key is Set
        -> EXPIRE key seconds – Set expiry in seconds
        -> PEXPIRE key ms – Set expiry in milliseconds
        -> EXPIREAT key timestamp – Expire at Unix time (sec)
        -> PEXPIREAT key ms_timestamp – Expire at Unix time (ms)
        -> TTL key – Time left (seconds)
        -> PTTL key – Time left (milliseconds)
        -> PERSIST key – Remove expiry

## Redis Database Management

    View Total Databases
        -> CONFIG GET databases – Shows how many databases are configured (default is 16).

    Switch Between Databases
        -> SELECT index – Switch to a database (e.g., SELECT 1 for DB 1).
        -> Databases are numbered from 0 to 15 by default.

    Count Keys in a Database
        -> DBSIZE – Shows the number of keys in the current DB.
        -> redis-cli -n 2 DBSIZE – Check keys in a specific DB (DB 2 here).

    Change Number of Databases
        -> Edit redis.conf:
            databases 4
        -> Limits Redis to databases 0–3.

    Key Tips
        -> Only numbered databases (no names).
        -> All DBs share the same memory.
        -> No memory tracking per DB.


## Redis Key Namespacing

    What -> Add prefixes to keys (e.g., user:1001:name) to group related data.

    Format -> <namespace>:<subcategory>:<id>

    Why -> Organizes data, avoids key conflicts, helps in debugging and deletion.

    Use -> KEYS user:* – Get all user keys

    Best Practices -> Use : as separator, keep it consistent and simple.

    Note -> Namespacing is manual — Redis doesn’t enforce it.

## Connecting redis in nodejs
    -> There are two packages for connecting redis:
        - ioredis
        - redis (officially by redis)

    -> Use the official node-redis
    ->  import { createClient } from "redis";
        const redisClient = await createClient().connect();

## RedisJSON Datatype

    What is it?
        -> Lets you store and manage JSON data in Redis.
        -> Use commands like JSON.SET, JSON.GET.

    Common Commands:

        Set JSON:
            -> JSON.SET user:1 $ '{"name":"Sahil","age":25}'

        Get JSON:
            -> JSON.GET user:1          // Full JSON  
            -> JSON.GET user:1 $.name   // ["Sahil"]

        Delete Field:
            -> JSON.DEL user:1 $.location

        Increment Number:
            -> JSON.NUMINCRBY user:1 $.age 1

        Array Ops:
            -> JSON.ARRAPPEND user:1 $.hobbies '"coding"'
            -> JSON.ARRPOP user:1 $.hobbies
            -> JSON.ARRLEN user:1 $.hobbies

        JSONPath Basics
        
            Path	      |     Gets...
        -> $	          |     Whole JSON
        -> $.name	  |     Name field
        -> $[0]	      |     1st array item
        -> $..name	  |     All name fields

    Note -> JSONPath always returns arrays like ["Sahil"].

## Redis for API Caching

    What: Store API responses in Redis to serve repeated requests faster.
    Why: Redis is fast (in-memory), supports TTL, and reduces DB load.

    How:
        Check Redis for cached data.
        If found → return it.
        If not → fetch from DB, store in Redis, then return.

    Key Features:
        Use unique cache keys (e.g., user:123)
        Set expiration (TTL)
        Invalidate cache when data changes

    Example:
        const cached = await client.get("key");
        if (cached) return JSON.parse(cached);
        // else fetch from DB and cache it


## Redis Search with JSON — Summary (with Index Example)

    Redis Search (RediSearch) allows full-text search and filtering on Redis JSON data using indexes.

    Index Types:
        TEXT → for searchable text (e.g. name)
        TAG → for exact matches (e.g. city, status)
        NUMERIC → for number ranges (e.g. age, price)
        GEO → Location-based queries (within radius, etc.)
        BOOLEAN (for Hash only as of now) → True/false values
        VECTOR → Index high-dimensional vectors for similarity search

    Example: Create Index on JSON
        FT.CREATE userIdx ON JSON PREFIX 1 user: SCHEMA $.name AS name TEXT $.city AS city TAG $.age AS age NUMERIC

        -> userIdx: index name
        -> PREFIX 1 user:: targets keys starting with user:
        -> Indexes name as TEXT, city as TAG, age as NUMERIC

    Search Examples:
        -> Exact match (city):
            FT.SEARCH userIdx "@city:{Delhi}"

        -> Age ≥ 28:
            FT.SEARCH userIdx "@age:[28 +inf]"

        -> Full-text name search:
            FT.SEARCH userIdx "@name:Sahil"

    Other Commands:
        -> FT._LIST → list all indexes
        -> FT.INFO userIdx → view index info
        -> FT.DROPINDEX userIdx → delete index only
        -> FT.DROPINDEX userIdx DD → delete index + data

## Redis VS Code Extension

    -> Install: Search “Redis” (by Redis Inc.) in VS Code extensions.
    -> Connect: Add host (localhost:6379) and optional password.
    -> Features:
        -> Browse and edit keys (JSON, strings, hashes, etc.)
        -> Run Redis commands with Redis: Open Terminal
        -> View and edit JSON with tree view
    -> Supports: RedisJSON, RediSearch, and more.

## Redis Advanced Search

    %word% → Fuzzy search (e.g. %Kumar% matches Kumaar)

    word1|word2 → Match any word (OR logic)

    LIMIT offset count → Paginate results

    -word → Exclude results with that word

    prefix* → Match words starting with prefix

    *suffix → Match words ending with suffix

## SCAN Command

    -> KEYS command is blocking and not recommended for production.
    -> Use SCAN — it's non-blocking and safe for large datasets.

    -> SCAN Command Syntax:

        SCAN <cursor> MATCH <pattern> COUNT <countValue> TYPE <typeName>

        -> cursor: Start with 0, used to fetch next batch.
        -> MATCH: Filter keys by pattern (e.g. user:*)
        -> COUNT: Approximate number of keys per batch
        -> TYPE: Filter by Redis data type (e.g. string, set, hash, zset, list, stream)

    -> If the returned cursor is 0, the scan is complete.
    -> Keys returned are not in order.

    -> Example: SCAN with All Options in Node.js

        let cursor = '0';

        do {
        const [nextCursor, keys] = await client.scan(cursor, {
            MATCH: 'user:*',
            COUNT: 10,
            TYPE: 'string'
        });

        console.log('Keys:', keys);
        cursor = nextCursor;
        } while (cursor !== '0');

## Redis List

    What is a Redis List?
        -> An ordered collection of strings — like a queue or stack.
        -> Supports adding/removing from both left and right.

    Common Commands:
        LPUSH mylist "a" → Add to left
        RPUSH mylist "b" → Add to right
        LPOP mylist → Remove from left
        RPOP mylist → Remove from right
        LRANGE mylist 0 -1 → Get all items
        LLEN mylist → List length
        LINDEX mylist 0 → Get item at inde
        LREM mylist 1 "a" → Remove item(s)
        LTRIM mylist 0 2 → Keep only index 0 to 2

## Redis Set

    What is a Redis Set?
        -> A set of unique, unordered strings
        -> Great for: tags, unique users, membership checks
        -> Fast operations: add, remove, check in O(1) time

    Common Commands
        SADD myset "apple" "banana" → Add items
        SREM myset "banana" → Remove item
        SMEMBERS myset → Get all items
        SISMEMBER myset "apple" → Check if exists (1 or 0)
        SCARD myset → Count elements
        SPOP myset → Remove & return random item
        SRANDMEMBER myset → Get random item (without removing)

## Redis Hash

    What is a Redis Hash?

    -> A Redis Hash is like a JavaScript object — it stores field-value pairs under one key.
    Great for user data, settings, or structured records.

    Common Commands
        HSET userHash name "Alice" age "25" → Add/set fields
        HGET userHash name → Get value of one field
        HGETALL userHash → Get all fields and values
        HMGET userHash name age → Get multiple values
        HDEL userHash age → Delete a field
        HEXISTS userHash name → Check if field exists
        HLEN userHash → Count fields
        HKEYS userHash → List all field names
        HVALS userHash → List all values
        HINCRBY userHash age 1 → Increment a number field

## Redis PUB/SUB

    What is Pub/Sub?

        -> Publish/Subscribe is a messaging pattern where:
            -> Publishers send messages to a channel
            -> Subscribers listen and receive those messages
        -> Both are decoupled (they don't know each other)
        -> Great for real-time and event-driven systems

    Redis Pub/Sub Basics

        SUBSCRIBE channel → Start listening
        PUBLISH channel message → Send message to subscribers
        UNSUBSCRIBE channel → Stop listening
        Messages are not saved — you must be online to receive them

    Node.js Example (using node-redis)

        const { createClient } = require('redis');

        const subscriber = createClient();
        const publisher = createClient();

        await subscriber.connect();
        await publisher.connect();

        await subscriber.subscribe('chat', (msg) => {
        console.log('Received:', msg);
        });

        await publisher.publish('chat', 'Hello!');

## Authentication in Redis

    -> Redis has no password by default (risky in production).
    -> Enable auth by editing config:
        sudo nano /etc/redis/redis.conf
    -> Set: requirepass yourPassword
    -> Restart Redis:
        sudo systemctl restart redis-server
    -> Use in CLI: AUTH yourPassword

    -> In NodeJS
        import { createClient } from 'redis';

        const client = createClient({
        password: 'yourStrongPassword123'
        });

        await client.connect();

## Redis Eviction Policy 

    -> Eviction policy decides which keys Redis deletes when memory is full.
    -> By default, Redis allows unlimited memory (noeviction).

    -> Set Memory Limit & Policy:

        -> In config file (permanent):
            maxmemory 100mb
            maxmemory-policy allkeys-lru

        -> At runtime (temporary):
            CONFIG SET maxmemory 100mb
            CONFIG SET maxmemory-policy allkeys-lru

    -> Common Policies:
        -> noeviction: ❌ No keys removed, throws error on write
        -> allkeys-lru: Removes least recently used key
        -> allkeys-lfu: Removes least frequently used key
        -> volatile-*: Only affects keys with expiry

    -> Use INFO memory to check usage
    -> Runtime settings reset on restart

