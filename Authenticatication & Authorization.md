### Section 14

## Intorduction to Authentication & Authorization

    Authentication
        -> Verifies who you are.
        -> Examples: Logging in with a password, Google OAuth, or fingerprint.
        -> Happens before authorization.
        -> Based on credentials like passwords, OTPs, or biometrics.
        -> Results in a session or token to prove your identity.

    Authorization
        -> Verifies what you can do.
        -> Examples:
            A user can view their own profile but not others’.
            Only an admin can delete users.
        -> Happens after authentication.
        -> Based on roles or permissions defined in the system.
        -> Determines whether access is granted or denied.

    Real-World Analogy
        -> Authentication is like showing your ID card at the building entrance.
        -> Authorization is like being allowed into certain rooms based on your role.

    Final Notes
        -> You must be authenticated before you can be authorized.
        -> Both are essential for building secure systems.

## Stateful Auth

    Authentication (Stateful)
        -> Uses sessions stored on the server (memory or database).
        -> After login, server creates a session ID and sends it as a cookie to the client.
        -> The client sends the cookie on each request.
        -> Cookies can be signed, HttpOnly, Secure, and expire after a time.

    Authorization (Stateful)
        -> Uses Role-Based Access Control (RBAC):
            Roles like admin, user, editor are stored on the server (in session or DB).
            Server checks the user's role on every request.

        -> May also use Access Control Lists (ACLs):
            Specific permissions are associated with users or roles.
            Checked by the server per request.

## Stateless Auth

    Authentication (Stateless)
        -> Uses tokens, often JWT (JSON Web Token).
        -> After login, the server sends a token to the client.
        -> Client stores it (in memory, secure storage, or cookie) and includes it in each request:
            Web: via HttpOnly cookie or memory.
            Mobile: via Authorization: Bearer <token> header.
        -> Server verifies the token on each request — no session is stored on server.

    Authorization (Stateless)
        Uses Claims-Based Authorization:
            -> Token contains claims like roles and permissions.
            -> Server uses this data to allow or deny access.
            -> No need to check DB or session.

    Key Notes
        -> Stateful: Server holds session info. More control but more memory usage.
        -> Stateless: Server stores nothing. Easier to scale, but less secure if not handled properly.
        -> In real-world apps, a hybrid approach is common (e.g., JWT for auth + refresh tokens + some server-side session control).
        -> Important: For sensitive browser or mobile apps, avoid full stateless auth (JWT-only) unless you can tolerate lower session control and some security risks.

## Cryptography Summary

    Cryptography secures data and ensures privacy, integrity, and authenticity.

    Encryption (Reversible)
        -> Converts readable data (plaintext) into unreadable form (ciphertext).
        -> Can be reversed using a key.
        -> Used when you need to retrieve original data.
        -> Common algorithms: AES, RSA (avoid DES).

    Hashing (Irreversible)
        -> Converts data into a fixed-size hash/digest.
        -> One-way — cannot be reversed.
        -> Used for verifying data or storing passwords.
        -> Common algorithms: SHA-256 (avoid MD5, SHA-1).
        -> Use salt to prevent attacks.

## Understanding Hashing – Summary

    What is Hashing?
        -> A process that converts input data (like text or files) into a fixed-size output using a hash function.
        -> Output is usually shown as a hexadecimal string, but its size is in bytes.

    Why is Hashing Used?

        ✅ Data Integrity – Check if data was modified.
        ✅ Version Control – Identify file or code changes.
        ✅ Digital Signatures – Ensure authenticity.
        ✅ Password Storage – Store passwords securely.
        ✅ Blockchain – Secure transactions and blocks.

    Key Features of Secure Hash Functions
        -> Deterministic – Same input gives same output.
        -> Fast – Quickly computes the hash.
        -> Irreversible – Can’t get original data from hash.
        -> Collision-resistant – Two inputs don’t produce same hash.
        -> Avalanche Effect – Tiny input change → very different hash.

    Recommended Hash Algorithms
        -> SHA-2 family (SHA-224, SHA-256, SHA-384, SHA-512): Secure and widely used.
        -> SHA-3 family: More modern, alternative to SHA-2, based on Keccak algorithm.

    Insecure / Outdated Algorithms
        -> MD5: 128-bit hash, broken, should not be used.
        -> SHA-1: 160-bit, vulnerable, deprecated.

    Important Terms
        -> Message: The original input.
        -> Hash/Digest: Fixed-size output from hash function.
        -> Hash Function: The algorithm used (e.g., SHA-256).
        -> Collision: When two inputs produce the same hash.
        -> Salt: Random value added to input to strengthen the hash.

    Hashing ≠ Encryption
        -> Hashing is one-way, used for verification.
        -> Encryption is reversible, used for data protection.
        -> Encoding is different too — it’s just for data formatting.

## Hashing using Crypto Module

    -> Use crypto.createHash(algorithm) to create a hash (e.g., 'sha256').
    -> Use .update(data) to add data for hashing.
        Accepted types: string, Buffer, TypedArray, DataView
    -> You can also chain multiple .update() calls to combine data.
    -> Use .digest(format) to get the final hashed output ('hex', 'base64', etc.).
    -> Once .digest() is called, the hash object can’t be reused.

    -> Example:
        const crypto = require('crypto');
        const hash = crypto.createHash('sha256')
                        .update('hello')
                        .update('world')
                        .digest('hex');

## How Git Uses Hashing — Summary

    Git uses the SHA-1 algorithm to uniquely identify content. But it doesn't hash raw file data directly — it uses a special format:
        <type> <length>\0<content>

    For files, the format is: blob lengthOfData\0fileData

    Why This Format?
        -> Ensures uniqueness between object types (blob, tree, commit).
        -> Adds integrity by including content length and type.
        -> Prevents collisions across different objects.

    Git uses the same pattern for:
        -> blob: file data
        -> tree: directory structure
        -> commit: commit metadata
        -> tag: tag info

## Digital Signature (Asymmetric approach)

    A digital signature ensures a document’s integrity, authenticity, and non-repudiation.

    The process:
        -> Hash the document using SHA-256 (or similar).
        -> Sign the hash using the sender's private key.
        -> Send the document + signature.

    The receiver:
        -> Hashes the received document.
        -> Decrypts the signature using the sender's public key.
        -> Compares the hashes.

    If even 1 bit of the document changes, the hash will change completely and the signature will fail to verify.

## Cookie Auth Summary

    On Login:
        -> Create payload → { id, expiry }
        -> Sign it using: sha256(secretKey + payload + secretKey)
        -> Encode payload (base64url) → make: encodedPayload.signature
        -> Set cookie uid=encodedPayload.signature

    On Each Request:
        -> Extract cookie → split into payload + signature
        -> Recreate hash → sha256(secretKey + payload + secretKey)
        -> Compare hashes → reject if mismatch
        -> Check expiry
        -> Find user by ID → attach to req.user

## MAC/HMAC Vs Digital Signature

    MAC (Message Authentication Code)
        -> A code to verify data integrity and authenticate the sender.
        -> Uses a secret key combined with the message.
        -> Both sender and receiver share the same secret key.
        -> Anyone with the key can create and verify the MAC.

    HMAC (Hash-based Message Authentication Code)
        -> A type of MAC that uses a cryptographic hash function (e.g., SHA-256).
        -> More secure than just hashing the key and message directly.
        -> Protects against certain attacks like length-extension.

    Digital Signature
        -> Uses asymmetric cryptography (private/public key pair).
        -> The sender signs a message with their private key.
        -> Anyone with the public key can verify the signature.
        -> Provides authenticity and non-repudiation (proof the sender signed).

    Differences between MAC/HMAC and Digital Signature
        -> MAC/HMAC uses a shared secret key; digital signatures use a key pair.
        -> MACs can be generated and verified by anyone with the key; signatures can only be generated by the private key holder.
        -> Digital signatures allow public verification without revealing private keys.
        -> MACs are mainly for ensuring integrity and authentication between trusted parties; digital signatures provide stronger guarantees including non-repudiation.

## Cookie Signing with Cookie-Parser

    -> Use middleware, cookieParser("secret") to enable signing.
    -> Set a signed cookie:
        res.cookie("uid", "value", { signed: true });
    -> Read signed cookie securely via:
        req.signedCookies.uid
    -> If tampered, the cookie becomes undefined(false).

## Hashing Passwords

    -> Never save passwords directly: If someone hacks your database, they’ll see all user passwords.
    -> Hashing turns passwords into scrambled values:
        Like turning hello123 into a94a8fe5ccb19ba61c4c0873d391e987.
    -> But there's a problem:
    -> If a user chooses a weak password (like 123456), hackers can guess it using rainbow tables (huge list of pre-hashed common passwords).

## Dictionary Attack:

    Tries common passwords from a list by hashing and comparing them to the target hash.

## Rainbow Attack:

    Uses a precomputed table (rainbow table) of hashes to quickly reverse hashes back to plaintext.

## Key Derivation Function (KDF)

    Purpose:
        -> Converts weak secrets (like passwords) into strong cryptographic keys.

    Why needed:
        -> Passwords are short and predictable — KDFs add salt, iterations, and complexity to resist attacks.

    How it works:
        -> Input (password) + Salt + Iterations → Secure Key

    Popular KDFs:
        -> PBKDF2 – HMAC-based, widely used
        -> bcrypt – Good for password hashing
        -> scrypt – Memory hard, protects against GPU attacks
        -> Argon2 – Modern, recommended for passwords
        -> HKDF – Used for key expansion (not passwords)

## Bcrypt

    Two NPM Packages:
        bcrypt → C++ based (faster, use in Node.js backend).
        bcryptjs → Pure JavaScript (use in browser or where native modules are not available).

    When to Use What:
        Node.js (server-side): Prefer bcrypt for performance.
        Browser (client-side): Use bcryptjs since it runs without native bindings.

    Common bcrypt Methods

        1. bcrypt.genSalt(rounds)
            -> Generates a unique salt.
            -> rounds defines the cost factor — higher means slower but more secure.
            -> Example:
                const salt = await bcrypt.genSalt(10);

        2. bcrypt.hash(password, saltOrRounds)
            -> Hashes the password with a salt.
            -> You can pass:
                -> a salt string: bcrypt.hash(password, salt)
                -> or rounds directly: bcrypt.hash(password, 10) (auto-generates salt)
            -> Example:
                const hashed = await bcrypt.hash("mypassword", salt);

        3. bcrypt.compare(plain, hashed)
            -> Compares a plain password with its hashed version.
            -> Returns true if matched.
            -> Example:
                const isMatch = await bcrypt.compare("mypassword", hashed);

## JWT (JSON Web Token)

    What is JWT?

    JWT is a secure, compact token used for authentication and authorization, containing 3 parts:
        Header: Token type & algorithm.
        Payload: Data (e.g., user ID, role).
        Signature: Verifies data integrity.

    Common JWT Methods

        ➤ jwt.sign(payload, secret, options)
            Creates a token.
            Example:
                jwt.sign({ userId: 1 }, 'secret', { expiresIn: '1h' });

        ➤ jwt.verify(token, secret)
            Verifies and decodes token.
            Example:
                jwt.verify(token, 'secret');

        ➤ jwt.decode(token)
            Decodes token without verifying.
            Example:
                jwt.decode(token);

## Don't use JWT (Watch video/ Read Article in free time)

## Session

    What is a Session?
        -> A session is a way for a server to remember a user across multiple requests.
        -> It works by giving the client a unique ID, which helps the server recognize the user.
        -> There are two types of sessions based on how and where data is stored.

    Stateless Sessions (e.g., JWT):
        -> All data is stored on the client.
        -> Server is stateless.
        -> ✅ Scalable
        -> ❌ Cannot revoke easily, must protect the token

    Stateful Sessions (e.g., Session ID + server storage):
        -> Data stored on the server (RAM, file, or database).
        -> Client only holds a session ID.
        -> Types:
            -> In-memory: Fast, but lost on restart.
            -> File-based: Persistent but slow.
            -> Database-backed: Scalable and persistent (used in production).

## ServerSide Session Type

    Server-Coupled Sessions (Stateful)

        These sessions store data on the same server handling the user.
        In-Memory Session
            -> Data is saved in the server’s RAM.
            -> Very fast but lost if the server restarts.
            -> Not usable with multiple servers (not scalable).
            -> Good only for small apps or testing.

        File-Based Session
            -> Data is saved in files on the server.
            -> More lasting than memory, but slower.
            -> Still tied to one machine.
            -> Not good for big or scalable apps.

    Server-Decoupled Sessions (Stateless)

        These sessions store data in a shared database, not the server.
        Database-Backed Session
            -> Data is saved in Redis, MongoDB, or SQL.
            -> All servers can access the same session data.
            -> Works well with many servers.
            -> Good for large, scalable apps.

## TTL Index in MongoDB

    A TTL (Time-To-Live) index automatically deletes documents after a set time.
    -> Works with Date fields only
    -> Use expireAfterSeconds to set the time
    -> MongoDB deletes expired data every 60 seconds
    -> Great for sessions, OTPs, cache, and logs
    -> No alerts or logs when deletion happens
    -> Example:
    db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })

## What is OAuth?

    OAuth is a way for apps to access your data without needing your password.
    For example, a new app can get your Google files without you giving it your Google password.

    How It Works
        -> You (the user) want to use an app that needs your data (like Google Drive files).
        -> The app sends you to Google’s login page to ask for permission.
        -> You log in and allow the app to access only certain data (like files or emails).
        -> Google gives the app a special key (called an access token).
        The app uses that key to get your data – but only what you allowed.

    Why OAuth is Good
        ✅ Safe – You don’t have to share your password.
        ✅ You stay in control – You choose what the app can see.
        ✅ You can stop access anytime from your account.

## What is OpenID Connect?

    -> OpenID Connect (OIDC) helps apps know who the user is — like their name, email, or profile picture.
    -> It works on top of OAuth 2.0, which is used to get access to data.
        OAuth 2.0 = What you can access
        OIDC = Who you are

    How It Works
        -> You click "Login with Google" or another provider.
        -> You're sent to a login page (Google, etc.).
        -> You log in and give permission.
        -> The app gets an ID token that says who you are.
        -> The app reads this token to get your name, email, etc.

    What’s in OpenID Connect
        -> ID Token = A small package (JWT) with your info.
        -> serInfo Endpoint = An API to get more details if needed.
        -> copes = openid, profile, email, etc., tell what info the app can get.

    Why It’s Useful
        ✅ Simple and safe login
        ✅ No password sharing
        ✅ Used by big names like Google, Microsoft


## Google OpenID 

    -> Setup openID on developer console.
    -> Make a request to google authorization server (Login & Allow consent).
    -> It will redirect to the client app with code (valid for few minutes).
    -> Use that code and make a fetch request to get and Information.

##  Verifying Google ID Token

    -> Google signs id_token using RS256 (RSA) algorithm.
    -> Google provides public keys at:
        https://www.googleapis.com/oauth2/v3/certs (🔁 keys rotate periodically).
    -> These keys are in JWK format — convert to PEM using jwk-to-pem since jsonwebtoken needs PEM.

    Token Verification Steps:
        -> Fetch keys from v3/certs.
        -> Extract kid from token header.
        -> Find matching JWK.
        -> Convert JWK → PEM.
        -> Use jwt.verify(token, pem, { algorithms: ["RS256"] }).
        -> After verifying, check:
            aud === your client ID
            
    -> Always verify both signature and audience to ensure the token is valid and intended for your app.
