### SECTION 17 SECURITY

## Why Web Security Is Important

    -> Web apps often handle private data like passwords, emails, and money details. If the app is not secure, hackers can steal data, cause damage, and break user trust.

    -> Why You Should Care About Security
        -> Keep user data safe
        -> Stop hackers from breaking in
        -> Keep your website running smoothly
        -> Follow rules and laws (like GDPR)
        -> Protect your brand’s reputation

    Common Dangers to Web Apps:

        -> SQL Injection – Hacker tricks the database using bad input.
        Fix: Use safe database queries.

        -> XSS (Cross-Site Scripting) – Bad scripts are run in the browser.
        Fix: Clean user input.

        -> CSRF – User is tricked into doing something without knowing.
        Fix: Use CSRF tokens.

        -> Weak Login Systems – Easy-to-guess passwords or session hacks.
        Fix: Use strong logins and 2FA.

        -> IDOR – Accessing other people’s data by changing URLs.
        Fix: Check user access for every request.

        -> Sensitive Data Leaks – Data is not encrypted or logged by mistake.
        Fix: Use HTTPS and encryption.

        -> Bad Settings – Default passwords, open ports, etc.
        Fix: Configure servers properly and update them.

        -> DoS/DDoS Attacks – Overloading the server so it crashes.
        Fix: Use rate limits and traffic filters.

        -> Old Libraries – Using code with known bugs.
        Fix: Update dependencies often.

        -> No Monitoring – Attacks go unnoticed.
        Fix: Add logs and alerts.

## Storing Data in .env File

    -> .env files store sensitive info like API keys and passwords.
    -> Keeps data secure and separate from your code.
    -> Use the dotenv package or load values natively in Node.js.
    -> Never upload .env files to GitHub – add them to .gitignore.
    -> In VITE, variable names must start with VITE_<KEY_Name>.

    -> Example:

        PORT=3000
        DB_URL=mongodb://localhost:27017/mydb
        JWT_SECRET=mySecret

## SQL Injection / NoSQL Injection

    Even though MongoDB doesn’t use SQL, it can still be hacked if you don’t handle user input properly. This is called NoSQL Injection.

    What Can Go Wrong?
        -> If you directly use user input in a query, a hacker can trick the database.
        -> Example (Bad code):

            User.findOne({ email: req.body.email, password: req.body.password });
            -> If someone sends special values like { "$ne": null }, they can log in without the correct password.

    How to Stay Safe
        -> Use validations – It checks that the input is in the right format.
        -> Check user input – Make sure it's a string and not an object.
        -> Don’t use raw JSON from users – Never trust JSON objects in requests.

## What is Zod?

    Zod is a JavaScript/TypeScript library used to check and validate data (like form inputs or API requests).

    Why Use Zod?
        -> Makes sure data is in the correct format
        -> Works great with TypeScript
        -> Easy to use and fast
        -> Catches invalid or missing data early

    Example:
        const userSchema = z.object({
            name: z.string(),
            age: z.number(),
        });

    Then you can validate data like this:
        userSchema.parse({ name: "Sahil", age: 22 }); // ✅ Valid
        userSchema.parse({ name: "Sahil", age: "twenty" }); // ❌ Error

    safeParse in Zod:

        -> safeParse is a method in Zod that lets you validate data safely without crashing your app.
        -> How it Works:
            Returns an object with { success: true, data } if valid
            Returns { success: false, error } if invalid

        -> Example:
            const result = userSchema.safeParse({ name: "Sahil", age: "twenty" });

            if (result.success) {
                console.log("✅ Valid:", result.data);
            } else {
                console.log("❌ Error:", result.error.issues);
            }

## Using Validators to prevent SQL Injection

    Mongoose Schema validates only while inserting and updating the document in the DB.
    For Validatiing the search query we should use ZOD.

## Same-Origin Policy (SOP)

    What is SOP?
        -> A browser security feature that restricts how scripts from one origin can access data from another origin.

    What is an Origin?
        -> Defined as: Protocol + Hostname + Port
        -> Any change in these three makes it a different origin.

    What SOP Blocks:
        -> Reading cookies, localStorage, or sessionStorage of another origin.
        -> Reading the response body of cross-origin fetch() or XMLHttpRequest.
        -> Accessing DOM of a cross-origin iframe.

    What SOP Allows:
        -> Sending requests to other origins (e.g., fetch, <img>, <script>).
        -> Loading images, styles, scripts, and fonts from other origins (read-only).
        -> Embedding content in cross-origin iframes (no access to internal content).

    Why SOP Exists:
        -> To protect users from malicious websites trying to steal data from other sites (like your bank or email).
        -> Prevents cross-site attacks like CSRF and data theft.

    Who Enforces SOP?
        -> The browser, automatically and always enabled.

    Can You Disable SOP?
        -> No. SOP is enforced in all modern browsers and cannot be turned off.

    How to Allow Cross-Origin Access (Safely):
        -> Use CORS (Cross-Origin Resource Sharing) — configured on the server.
        -> Use postMessage API — for secure communication between iframes/windows.

## What is XSS?

    Cross-Site Scripting (XSS) is a vulnerability where attackers inject malicious JavaScript into a web page, which then runs in the browser of other users.

    Why It’s Dangerous ?
        -> Can steal cookies, sessions, and sensitive data
        -> Can impersonate users or perform actions on their behalf
        -> May lead to account takeovers or site defacement

    How It Happens ?
        -> A site displays user input (like a comment or form value) without sanitizing it
        -> The attacker injects a script
        -> Other users visiting that page run the script unknowingly

    How to Prevent It ?
        -> Sanitize or escape all user input
        -> Use tools like DOMPurify
        -> Avoid unsafe methods like innerHTML, eval, etc.
        -> Use Content Security Policy (CSP) to block unwanted scripts

## XSS Sanitization with DOMPurify

    Why? To protect against XSS, always sanitize HTML from users or backend before rendering.

    Tool: Use dompurify for sanitizing HTML.

    Server-Side (Node.js)
        -> Needs a DOM-like environment → install jsdom.
        -> Eg:
            import createDOMPurify from 'dompurify';
            import { JSDOM } from 'jsdom';

            const DOMPurify = createDOMPurify(new JSDOM('').window);
            const clean = DOMPurify.sanitize(dirtyHTML);

    Client-Side (React)
        -> Directly use dompurify to sanitize and safely render HTML:
        -> Eg:
            import DOMPurify from 'dompurify';

            const SafeHTML = ({ html }) => (
            <div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(html) }} />
            );

## Types of XSS Attack

    Cross-Site Scripting (XSS) allows attackers to inject malicious JavaScript into a webpage. There are 4 main types:

    Stored XSS (Persistent)
        -> Script is saved on the server (e.g., in a comment)
        -> Runs for every user who views it
        -> 🔥 High risk

    Self-XSS
        -> Attacker tricks user into pasting code in their browser console
        -> ⚠️ Medium risk

    Reflected XSS (Non-Persistent)
        -> Script comes from the URL or form and reflects in the response
        -> Runs immediately when the page loads
        -> ⚠️ Medium–High risk

    DOM-Based XSS
        -> Happens in client-side JavaScript (e.g., from innerHTML)
        -> No server involved
        -> ⚠️ Medium–High risk

## CSP (Content Security Policy)

    What is CSP?
        -> Content Security Policy (CSP) is a security feature that controls what content (scripts, styles, images, etc.) your website is allowed to load. It helps protect against XSS, clickjacking, and code injection.

    Why Use It?
        -> Blocks untrusted scripts and resources
        -> Prevents inline script execution
        -> Reduces risk of browser-based attacks

    How to Use
        -> Set CSP via HTTP headers or <meta> tags:
            Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted.com

    Best Practices
        -> Use 'self' for trusted sources
        -> Avoid 'unsafe-inline' and 'unsafe-eval'
        -> Use nonces or hashes for inline scripts
        -> Set frame-ancestors 'none' to block iframes

    Test & Tools
        -> Use browser dev tools
        -> Try Google CSP Evaluator
        -> Use helmet in Express for easy setup

    Summary
        -> CSP adds a strong layer of protection to your website by allowing only safe content to run. It’s one of the easiest ways to defend against common web attacks.

## How to Report CSP Violations

    1. Add Reporting in CSP Header
    Use report-uri or report-to:
    Content-Security-Policy: default-src 'self'; report-uri /csp-report

    2. Browser Sends Violation Reports
        Sends JSON to your endpoint when a CSP rule is violated.

    3. Create Server Endpoint to Receive Reports
        Example (Express.js):

        app.post('/csp-report', express.json({ type: "application/csp-report"}), (req, res) => {
            console.log(req.body);
            res.sendStatus(204);
        });

## Use Inline Scripts with Hashes (CSP)

    1. Write the Inline Script
        <script>console.log("Hello CSP!");</script>

    2. Generate SHA-256 Hash
        In Node.js:
        crypto.createHash('sha256').update('console.log("Hello CSP!");').digest('base64');

    3. Add Hash to CSP Header
        Content-Security-Policy: script-src 'self' 'sha256-<your-hash>';

    4. Keep Script Exactly the Same
        Even a small change breaks the hash match.

## Use Inline Scripts with Nonce (CSP)

    1. Generate a Unique Nonce (Per Request)
        Example in Node.js:
        const nonce = crypto.randomBytes(16).toString('base64');

    2. Add Nonce to CSP Header
        Content-Security-Policy: script-src 'self' 'nonce-<your-nonce>';

    3. Use the Same Nonce in Your Script Tag
        <script nonce="your-nonce">
        console.log("Inline script with nonce!");
        </script>

    4. The browser only runs scripts that match the nonce from the CSP.

## Clickjacking

    🔹 What is Clickjacking?

        Clickjacking (short for click hijacking) is a malicious technique where an attacker tricks a user into clicking on something different from what they perceive.

        It usually involves embedding a legitimate website (like a banking site, “Like” button, or video player) inside an invisible or disguised frame (<iframe>), so when the user clicks, they are actually interacting with the attacker’s chosen target.

        👉 In simple words: You think you’re clicking a harmless button, but you’re actually clicking something hidden.

    🔹 How Clickjacking Works

        -> Attacker sets up a malicious webpage.

        -> On this page, they load the victim’s site (like a bank transfer form, or social media “Like” button) inside a transparent iframe.

        -> The iframe is positioned so that when the user clicks a visible button on the attacker’s page, they are unknowingly clicking the hidden iframe element.

        -> The attacker manipulates the action – e.g., transferring money, liking a page, or enabling webcam permissions.

    🔹 Real-World Examples of Clickjacking

        -> Facebook Likejacking (2010s)
        Attackers tricked users into “liking” pages by hiding the Facebook Like button under fake video play buttons. This boosted the popularity of pages without the user’s consent.

        -> Twitter Tweetjacking
        Some websites made users unknowingly tweet links by overlaying invisible tweet buttons.

        -> Banking Fraud
        A user might be tricked into clicking a hidden "Transfer Money" button on their bank site while believing they are playing a game or pressing another button.

        -> Adobe Flash Camera/Microphone Settings (2008)
        Attackers exploited clickjacking to make users enable their webcam/microphone without realizing it.

    🔹 How to Prevent Clickjacking

        For Developers / Website Owners

        1) X-Frame-Options HTTP Header

            DENY → Page cannot be displayed in a frame.
            SAMEORIGIN → Page can only be displayed in a frame on the same origin.
            ALLOW-FROM https://example.com/ → Allow framing only from a trusted domain.

        2) Content-Security-Policy (CSP) with frame-ancestors:

            Content-Security-Policy: frame-ancestors 'self' https://trusted.com
            More flexible and modern replacement for X-Frame-Options.

        3) Frame Busting Scripts (legacy, less reliablem, older method)

            JavaScript to detect if a page is inside a frame and break out:

            if (top !== self) {
                top.location = self.location;
            }

        For Users:
            -> Use updated browsers (modern browsers block many iframe exploits).
            -> Use browser extensions like NoScript or uBlock Origin that block clickjacking attempts.
            -> Avoid clicking on suspicious links and “free” video play buttons.

## Domain Attribute

    ℹ️ Default (No Domain Attribute)
        -> Cookie = host-only
        -> Sent only to the exact domain that set it.
        -> Example: set by www.example.com → works only for www.example.com.

    ℹ️ With Domain Attribute
        -> Cookie shared with domain + all subdomains.
        -> Example:
            Domain=example.com → works for example.com, www.example.com, shop.example.com.

    ℹ️ Rules
        -> Must be the same domain or parent domain.
        -> Cannot be a public suffix (like .com, .org).
        -> Prevents setting cookies for unrelated domains.

    ℹ️ Real-World Uses
        -> Banking sites → one session across www.bank.com, app.bank.com.
        -> SSO systems → login cookie shared across multiple subdomains.
        -> Risk → any subdomain can access cookies → possible session hijacking if one subdomain is compromised.

## Top Level Navigation

    ⚡ What is it?
        -> When a webpage is loaded directly in the main browser window/tab (the address bar URL).
        -> Not inside an iframe, embedded element, or popup.

    ⚡ How does it work?
        -> When you click a link or type a URL, the browser loads that site in the topmost browsing context (the tab).
        -> Browser treats this as a full page navigation.

    ⚡ Why is it important?
        -> Security: Cookies with SameSite=Lax are only sent during top-level navigations, protecting against CSRF.
        -> Permissions: APIs like camera/microphone often require top-level context.
        -> User Control: Ensures the user is knowingly visiting the site (not hidden in an iframe).

    ⚡ Examples
        ✅ Visiting https://example.com directly in your browser → Top-level navigation.
        ❌ example.com loaded inside an iframe on attacker.com → Not top-level navigation.
        ✅ Clicking a Google search result that loads news.com in the tab → Top-level navigation.

## SameSite Cookie Attribute

    ⚡ What is it?
        -> A cookie setting that decides when cookies are sent with requests.
        -> Helps protect against CSRF attacks.

    ⚡ Why it matters?
        -> Before → Browsers always sent cookies (even from other sites).
        -> Hackers exploited this to perform unwanted actions (like money transfer).
        -> SameSite lets developers control cookie sending rules.

    ⚡ History
        Before 2016 → No SameSite, cookies sent everywhere ❌
        2016 → Chrome added SameSite (Strict/Lax), optional, default = None
        2020 → Default changed → cookies without SameSite = Lax ✅

    ⚡ Values of SameSite

        -> Strict
            Cookies sent only from same-site.
            ✅ Strongest protection.
            ❌ Can break login/redirect flows.

        -> Lax (Default)
            Cookies sent on same-site requests ✅
            Sent on top-level GET (like link click) ✅
            ❌ Not sent on cross-site POST/PUT/DELETE.
            ✅ Balance of security + usability.

        -> None
            Cookies sent on all requests.
            Must use Secure (HTTPS).
            ❌ No CSRF protection.
            Needed for SSO, cross-site iframes.

    ⚡ Security Implications

        Mode	CSRF Protection	    Best Use Case
        Strict	✅ Strongest       Banking, admin dashboards
        Lax	    ✅ Good enough	  Most web apps (default today)
        None	❌ None	          SSO, third-party embeds

## SameSite Attribute with Fetch()

    Strict → never sent cross-site.
    Lax → not sent cross-site (except 2-min POST grace period in Chrome).
    None; Secure → always sent cross-site.

## CSRF Attack (Cross-Site Request Forgery)

    ⚡ What it is?
        -> CSRF is when a hacker tricks your browser into sending a request you didn’t intend.
        -> Since your browser has your cookies, the malicious request looks legit to the server.

    ⚡ How it works (Step by Step)
        -> You log in to your bank (or any site) → Browser stores a session cookie.
        -> You visit a malicious site (attacker’s blog, ad, or email link).
        -> That site secretly makes a request to your bank like:
            <img src="https://mybank.com/transfer?to=attacker&amount=1000" />
        -> Browser automatically attaches your bank’s cookies.
        -> Bank receives the request → sees valid cookie → assumes it’s you → money transferred.

    ⚡ Why it works?
        -> Browsers auto-attach cookies to requests (same-site and cross-site).
        -> The bank doesn’t know if the request came from you or a hacker’s site.

    ⚡ Real-World Examples
        -> Money transfer without permission 💰
        -> Changing your email/password on a site 🔑
        -> Submitting a malicious form on your behalf

    ⚡ How to Prevent CSRF
        SameSite Cookies → restrict cookie sending in cross-site requests.
        CSRF Tokens → server generates a random token per session/request; form submissions must include it.
        Double Submit Cookie → send CSRF token in both cookie + request body and compare.
        Check Referer/Origin Header → validate the request came from the right site.
        Use Secure + HttpOnly Cookies.

## DoS Attack

    ⚡ What is a DoS Attack?
        -> Denial of Service (DoS) = An attack where the goal is to make a website, server, or network unavailable to its users.
        -> Instead of stealing data, attackers overwhelm the system so that real users can’t access it.

    ⚡ How it Works

        -> Flooding with Requests
            The attacker sends too many fake requests at once (like refreshing a site millions of times).
            The server gets overloaded and can’t handle real users.

        -> Resource Exhaustion
            Attackers may exploit weak spots (CPU, memory, bandwidth).
            Example: Making a database do heavy queries repeatedly until it crashes.

        -> Exploiting Vulnerabilities
            Sometimes attackers use bugs in software to crash the service instead of flooding it.

    ⚡ Types of DoS Attacks
        Volumetric Attacks → Overwhelm bandwidth (like flooding a pipe with water).
        Protocol Attacks → Exploit weaknesses in network protocols (e.g., SYN flood).
        Application-Layer Attacks → Target the app itself (e.g., sending repeated search requests that are expensive to process).

    ⚡ Real-World Example
        -> Imagine a shop with only 1 cashier.
        -> An attacker hires 1,000 fake customers to stand in line.
        -> Real customers can’t buy anything → service is denied.

    ⚡ Defense Against DoS

        -> Rate limiting (limit how many requests per second each user can make).
        -> Firewalls & Intrusion Detection (block suspicious traffic).

## Rate Limiting

    ⚡ What is Rate Limiting?
        -> Rate Limiting = Restricting how many times a user/client can call an API within a specific time frame.
        -> Example: "A user can only call the /login API 5 times per minute."
        -> It prevents abuse, DoS, brute-force attacks, and unnecessary server load.

    ⚡ Why APIs Need Rate Limiting
        -> Prevents brute force attacks (e.g., guessing passwords).
        -> Protects against DoS/DDoS attacks (too many requests).
        -> Ensures fair usage among users.
        -> Saves server resources & bandwidth.

    ⚡ Common Strategies for Rate Limiting

        Fixed Window
            -> Example: Allow 100 requests per minute.
            -> Simple but can cause bursts at window reset.
            -> Eg. "You can withdraw ₹10,000 per day from ATM."

        Sliding Window / Rolling Window
            -> Looks at last X seconds/minutes dynamically.
            -> More accurate and smooth.
            -> Eg. "In the last 24 hours, you can only withdraw ₹10,000." 

        Token Bucket
            -> Each request consumes a “token.”
            -> Tokens refill at a set rate (like filling a bucket with drops).
            -> Flexible — allows short bursts but controls long-term usage.
            -> Eg. "Your internet plan allows bursts of high speed (using tokens), but overall speed is limited."

        Leaky Bucket
            -> Requests are processed at a fixed rate, excess gets dropped.
            -> Smooths out request spikes.
            -> Eg. "Queue at a toll booth, cars leave at a fixed rate; if too many arrive, they wait or get blocked."

    ⚡ Example in Real Life
        -> ATM: You can’t withdraw money more than a limit per day.
        -> API: You can’t call /checkout more than 10 times per minute.

## DDoS Attack

    ⚡ What is a DDoS Attack?
        -> DoS (Denial of Service): An attack where one computer floods a server with too many requests, making it slow or crash.
        -> DDoS (Distributed DoS): Same thing, but done using many computers (often thousands, called a botnet) all at once.
        -> Goal → Overload the server, network, or application so real users can’t use it.

    ⚡ How It Works
        🔗 Botnet creation → Attacker infects many computers/devices (IoT, PCs) with malware.
        📡 Command & Control (C&C): Attacker controls these devices remotely.
        🚀 Attack launch: All infected devices send massive requests/data to the target server.
        💥 Impact: Server gets overwhelmed → crashes, slows down, or becomes unavailable.

    ⚡ Types of DDoS Attacks
        -> Volumetric Attacks: Flood server with high traffic (e.g., UDP flood, ICMP flood).
        -> Protocol Attacks: Exploit network layer weaknesses (e.g., SYN flood, Ping of Death).
        -> Application Layer Attacks: Target specific services like HTTP requests to overload the web server (e.g., sending millions of fake login requests).

    ⚡ Real-World Example
        A shopping website during a sale gets millions of fake requests, making it impossible for real customers to buy anything.

    ⚡ How to Protect Against DDoS
        -> Use CDNs & WAFs (Cloudflare, Akamai, AWS Shield) to absorb traffic.
        -> Add rate limiting.
        -> Use load balancers to spread requests across multiple servers.
        -> Monitor traffic for suspicious spikes.

## Important Security Headers

    Content-Security-Policy (CSP) → Tells browser what scripts/images/styles it can load → Stops XSS.
    X-Frame-Options → Blocks your site from opening inside an iframe → Stops clickjacking.
    X-Content-Type-Options → Stops browser from guessing file type → Prevents script tricks.
    Strict-Transport-Security (HSTS) → Forces site to always use HTTPS → More secure.
    Referrer-Policy → Controls how much user info is shared when clicking links → Protects privacy.
    Permissions-Policy → Controls who can use features like camera, mic, location → Safer apps.
    COOP / CORP / COEP → Advanced headers → Isolate your site from other origins → Prevent cross-site attacks.

## Helmet Library

    -> Helmet is a security middleware for Express.js.
    -> It sets important HTTP headers automatically → makes your app safer.
    -> Think of it as a helmet for your app 🪖 → protects against common web attacks.

    🛡 What Helmet Does

        When you use helmet(), it enables many protections:
        -> CSP (Content-Security-Policy) → Control what resources (scripts/images) can load.
        -> HSTS (Strict-Transport-Security) → Force HTTPS only.
        -> X-Frame-Options → Stop clickjacking.
        -> X-Content-Type-Options → Prevent file type sniffing.
        -> Referrer-Policy → Control what referrer info is sent.
        -> Permissions-Policy → Restrict features like camera, mic, geolocation.

    ⚡ Example:
        const express = require("express");
        const helmet = require("helmet");

        const app = express();

        // Enable helmet
        app.use(helmet());

        // Example route
        app.get("/", (req, res) => {
        res.send("Hello, your app is safer with Helmet! 🪖");
        });

        app.listen(3000, () => {
        console.log("Server running on http://localhost:3000");
        });

## Throttling

    ⚡ What is Throttling?
        -> Throttling means slowing down or limiting how many requests/actions a user or system can do in a time period.
        -> It’s like a speed breaker for your API or app to avoid overload.

    ⚡ Why do we use Throttling?
        -> Protects servers from too many requests at once.
        -> Stops abuse (bots, scrapers, brute-force).
        -> Keeps things fair so one user can’t block others.
        -> Saves costs (CPU, memory, bandwidth).

    ⚡ How does Throttling work? (Simple view)
        -> You set a limit: e.g., “100 requests per minute.”
        -> If someone sends requests too fast:
            -> You delay extra requests, or
            -> You reject them with an error, or
            -> You queue them to run later at a safe speed.

    ⚡ Where is Throttling used?
        -> APIs (public/private).
        -> Login and OTP endpoints (to stop brute-force).
        -> Search and filter endpoints (they’re heavy).
        -> Payments/checkout (prevent spam and errors).
        -> File uploads/downloads (bandwidth control).
        -> Mobile apps and browsers (limit rapid clicks/calls).

## Benefits of throttling

    ⚡ Protects servers
        -> Stops sudden floods of requests that can crash or slow down your system.

    ⚡ Prevents abuse
        -> Blocks bots, scrapers, or attackers from spamming your API or login system.

    ⚡ Fair usage
        -> Ensures one user doesn’t hog all the resources, keeping it fair for everyone.

    ⚡ Better performance
        -> Keeps traffic flow smooth and predictable, so your app runs faster.

    ⚡ Cost saving
        -> Reduces extra load on servers, network, and database, saving infrastructure costs.

    ⚡ Improves security
        -> Limits brute-force login attempts, DoS/DDoS effects, and other automated attacks.

    ⚡ User experience
        -> By slowing down instead of blocking completely, real users don’t feel punished.

    ⚡ Scalability
        -> Makes it easier to handle sudden spikes in traffic (like during sales or events).

## Drawback of throttling

    ⚡ May block real users
        -> Sometimes genuine users get blocked if they send too many requests (e.g., fast typing or slow internet causing retries).

    ⚡ Slower experience
        -> If limits are too strict, users may face delays or “try again later” errors.

    ⚡ Hard to set correct limits
        -> Too high → no protection.
        -> Too low → frustrates users.

    ⚡ Doesn’t stop all attacks
        -> Attackers can bypass limits by using many IPs (DDoS or botnets).

    ⚡ Extra complexity
        -> Needs tracking, storage (counters/tokens), and sometimes extra infrastructure like Redis.

    ⚡ Resource overhead
        -> Checking and enforcing limits consumes server memory/CPU.

    ⚡ Not suitable for all apps
        -> Real-time apps (like chat or gaming) can break if throttling is too aggressive.

## Adaptive Vs Dynamic Throttling

    ⚡ Adaptive Throttling
        -> Adjusts limits based on system load or health.
        -> If servers are under heavy load, limits become stricter.
        -> If traffic is normal, limits are relaxed.
        -> Example: API allows 100 requests/sec normally, but reduces to 20/sec when CPU usage is high.

    ⚡ Dynamic Throttling
        -> Limits are not fixed, they change based on user behavior or patterns.
        -> Example:
            -> A trusted user making normal requests = higher limit (e.g., 200/min).
            -> A suspicious user sending bursts = lower limit (e.g., 10/min).
            -> Used with AI/ML sometimes to detect abuse.

## Rate Limiting Vs Throttling

    ⚡ Rate Limiting
        -> Fixes a maximum number of requests in a time window.
        -> Example: Max 100 requests per minute.
        -> If limit is reached → further requests are blocked/rejected.
        -> Goal = protect backend APIs from abuse.

    ⚡ Throttling
        -> Controls the speed of requests (not just the number).
        -> Example: Allow 5 requests/sec, extra ones are delayed/queued, not always blocked.
        -> Goal = smooth traffic flow & prevent server overload.

## Dependency Vulnerability

    ⚡ What is Dependency Vulnerability?
        -> When your project uses external libraries, frameworks, or packages, those are called dependencies.
        -> If one of those dependencies has a security flaw, your app also becomes vulnerable.
        -> Example: If you use an outdated version of express or lodash that has a known bug, attackers can exploit it through your app.

    ⚡ Why It Happens?
        -> Using old or outdated packages.
        -> Depending on too many third-party libraries.
        -> Not checking for known vulnerabilities.
        -> Trusting unverified open-source packages.

    ⚡ Risks
        -> Data leaks (sensitive info exposed).
        -> Remote code execution (hackers run code on your server).
        -> Malware injection (backdoors added in supply chain).
        -> Service crash (DoS).

    ⚡ How to Prevent It?
        -> Keep dependencies updated (use latest stable versions).
        -> Use package manager tools:
            npm audit (Node.js)
        -> Use trusted sources (official repos).
        -> Remove unused dependencies.
        -> Use dependency scanning tools (e.g., Snyk, Dependabot, OWASP Dependency-Check).
