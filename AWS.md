## What is AWS S3 Bucket ?

    -> An AWS S3 Bucket is basically a container for storing data in the cloud using Amazon Simple Storage Service (S3).

    -> Think of it like a main folder on your computer, but instead of being stored locally, it lives in the AWS cloud. Inside a bucket, you can put files (called objects) like images, videos, backups, or documents.

    Key Points:
        -> Bucket = Container → Every object (file) is stored in a bucket.
        -> Objects = Files → Each has data + metadata (info about the file).
        -> Scalable → You can store unlimited data, from MBs to petabytes.
        -> Durable & Available → Data is automatically backed up across multiple AWS locations.
        -> Permissions → You control who can access your bucket (private, public, or restricted).
        -> Versioning → Keeps old versions of files, so you can roll back if needed.
        -> Static Website Hosting → You can even host a simple website (HTML/CSS/JS) from it.

    Example:
        -> Create a bucket → my-app-bucket.
        -> Upload a file → logo.png.
        -> S3 generates a URL: https://my-app-bucket.s3.amazonaws.com/logo.png

    -> Depending on settings, this link may be private (only you) or public (anyone with the link).
    -> In short: An S3 bucket is like your cloud drive on AWS, but with enterprise-level durability, scalability, and security.

## Benefits of AWS S3 over Local/Own Server Storage

    1. Scalability
        Own server → You’re limited by the hard drive capacity. If it fills up, you need to buy more hardware.
        S3 → Unlimited storage. You can start with 1 MB and scale up to petabytes without managing hardware.

    2. Durability & Reliability
        Own server → Risk of hard drive failure, data loss, or corruption.
        S3 → Designed for 99.999999999% (11 nines) durability – your data is automatically replicated across multiple AWS data centers.

    3. Availability
        Own server → If your server is down (power failure, hardware crash), files become unavailable.
        S3 → Highly available; AWS ensures data can be accessed anytime from anywhere.

    4. Cost Efficiency
        Own server → Requires upfront investment (hardware, maintenance, cooling, electricity, staff).
        S3 → Pay-as-you-go. You only pay for the storage and data transfer you use. No maintenance costs.

    5. Security
        Own server → You must manage firewalls, encryption, access control, backups yourself.
        S3 → Built-in encryption, access policies, IAM roles, logging, versioning. Meets compliance standards (HIPAA, GDPR, etc.).

    6. Accessibility
        Own server → Usually only accessible on the local network unless you configure a web server, domain, etc.
        S3 → Accessible globally with just a URL or API request. Easy integration with apps/websites.

    7. Backup & Versioning
        Own server → Manual backups; if something is overwritten/deleted, recovery is tough.
        S3 → Versioning keeps old copies automatically. Data lifecycle rules can move older files to cheaper storage (Glacier).

    8. Performance
        Own server → Limited by your internet speed and server resources.
        S3 → High-performance data access with CDN support (via CloudFront) for global low-latency delivery.

    9. Disaster Recovery
        Own server → If the server is destroyed (fire, theft, flood), data may be gone unless you had an external backup.
        S3 → Multi-region replication ensures your data is safe even in case of regional outages.

## What is AWS CLI ?

    -> AWS CLI (Command Line Interface)
    -> The AWS CLI is a tool that lets you manage AWS services from your command line or terminal instead of always using the AWS web console.
    -> It’s like a shortcut to control AWS using commands.

    🔑 Key Points
        -> Full form: AWS Command Line Interface.
        -> Cross-platform: Works on Windows, macOS, and Linux.
        -> Uses your AWS credentials (Access Key & Secret Key) to authenticate.
        -> Supports all major AWS services – S3, EC2, IAM, Lambda, DynamoDB, etc.
        -> Automation-friendly: You can script tasks with it (using bash, PowerShell, etc.).

    🛠️ Example Commands
        -> Check AWS version: aws --version
        -> List all S3 buckets: aws s3 ls
        -> Upload a file to S3 bucket: aws s3 cp resume.pdf s3://my-bucket-name/
        -> Start an EC2 instance: aws ec2 start-instances --instance-ids i-1234567890abcdef0

    ⚡ Benefits
        -> Faster than console: No need to click around in the AWS web UI.
        -> Automatable: You can run scripts for repetitive tasks (like backups).
        -> Remote management: Control AWS resources from anywhere with terminal access.
        -> Integration: Can be combined with CI/CD pipelines, cron jobs, or DevOps tools.

## 🚀 AWS S3 CLI Cheat Sheet

    1. Bucket Commands
        List buckets → aws s3 ls
        Create bucket → aws s3 mb s3://bucket-name
        Delete bucket → aws s3 rb s3://bucket-name
        Force delete (with contents) → aws s3 rb s3://bucket-name --force

    2. Upload & Download
        Upload file → aws s3 cp file.txt s3://bucket/
        Upload folder → aws s3 cp ./folder s3://bucket/ --recursive
        Download file → aws s3 cp s3://bucket/file.txt ./local.txt
        Download folder → aws s3 cp s3://bucket/folder ./local --recursive

    3. Sync
        Local → S3 → aws s3 sync ./local s3://bucket/
        S3 → Local → aws s3 sync s3://bucket/ ./local

    4. List Contents
        Bucket files → aws s3 ls s3://bucket/
        Folder files → aws s3 ls s3://bucket/folder/

    5. Delete
        Delete file → aws s3 rm s3://bucket/file.txt
        Delete folder → aws s3 rm s3://bucket/folder/ --recursive

    6. Move / Rename
        Rename file → aws s3 mv s3://bucket/old.txt s3://bucket/new.txt
        Move folder → aws s3 mv s3://bucket/folder1/ s3://bucket/folder2/ --recursive

## AWS S3API CLI Cheatsheet

    🪣 BUCKET OPERATIONS
    ------------------------------

    -> List all buckets
    aws s3api list-buckets

    -> Create a bucket (us-east-1)
    aws s3api create-bucket --bucket my-bucket-name --region us-east-1

    -> Create a bucket (other regions, e.g., ap-south-1)
    aws s3api create-bucket --bucket my-bucket-name --region ap-south-1 --create-bucket-configuration LocationConstraint=ap-south-1

    -> Delete a bucket
    aws s3api delete-bucket --bucket my-bucket-name

    📄 FILE & FOLDER (OBJECT) OPERATIONS
    ------------------------------

    -> List files (objects)
    aws s3api list-objects-v2 --bucket my-bucket-name

    -> List only a specific folder
    aws s3api list-objects-v2 --bucket my-bucket-name --prefix folder1/

    -> Upload a file
    aws s3api put-object --bucket my-bucket-name --key folder1/file.txt --body ./localfile.txt

    -> Download a file
    aws s3api get-object --bucket my-bucket-name --key folder1/file.txt ./localfile.txt

    -> Delete a file
    aws s3api delete-object --bucket my-bucket-name --key folder1/file.txt

    -> Rename (Move) a file
    # Step 1: Copy file with new name
    aws s3api copy-object --bucket my-bucket-name --copy-source my-bucket-name/folder1/old.txt --key folder1/new.txt

    # Step 2: Delete old file
    aws s3api delete-object --bucket my-bucket-name --key folder1/old.txt

## Managing Bucket Policy using AWS S3 API

    -> Get Public Access Block Configuration
    aws s3api get-public-access-block --bucket my-bucket-name

    -> Delete Public Access Block Configuration
    aws s3api delete-public-access-block --bucket my-bucket-name

    -> Put Public Access Block Configuration

        🔒 Block Public Access (Recommended for Private Buckets)
        aws s3api put-public-access-block --bucket my-bucket-name --public-access-block-configuration '{
            "BlockPublicAcls": true,
            "IgnorePublicAcls": true,
            "BlockPublicPolicy": true,
            "RestrictPublicBuckets": true
        }'

        🔓 Unblock Public Access (For Public Websites)
        aws s3api put-public-access-block --bucket my-bucket-name --public-access-block-configuration '{
            "BlockPublicAcls": false,
            "IgnorePublicAcls": false,
            "BlockPublicPolicy": false,
            "RestrictPublicBuckets": false
        }'

    -> Get Bucket Policy
    aws s3api get-bucket-policy --bucket my-bucket-name

    -> Put (Set/Update) Bucket Policy
    aws s3api put-bucket-policy   --bucket my-bucket-name   --policy file://policy.json

        Example policy.json for public read
        {
        "Version": "2012-10-17",
        "Statement": [
            {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::my-bucket-name/*"
            }
        ]
        }

    -> Delete Bucket Policy
    aws s3api delete-bucket-policy --bucket my-bucket-name

## AWS Cloud Shell

    What is it?

    -> A pre-authenticated, browser-based terminal available directly from the AWS Management Console.
    It comes with the AWS CLI pre-installed and configured for your logged-in AWS account.
    You don’t need to set up credentials, profiles, or install anything locally.

    Key Features:
        No setup required → Works directly in your browser.
        Pre-installed tools
            → AWS CLI
            → AWS SDKs
            → Common Linux utilities (git, python, unzip, etc.)

        Persistent storage
            → Each region gives you 1 GB of persistent storage (/home/cloudshell-user/).
            → This storage remains even after sessions end.

        Secure & Region-specific 
            → CloudShell automatically uses the IAM identity you logged in with.
            → The shell environment is specific to the AWS region you launch it in.

        No extra cost → Free to use, you only pay for the AWS resources you create/consume.

    When to Use CloudShell
        -> Quick testing of AWS CLI commands.
        -> Managing AWS resources without installing CLI locally.
        -> Running scripts/tools in the AWS environment.
        -> Sharing a consistent environment across teams.

## What is a Signed URL in AWS S3?

    -> A Signed URL (also called a Pre-signed URL) is a temporary, secure link that allows someone to access a private object in an S3 bucket without needing AWS credentials.
    -> By default, S3 objects are private. If you want to share them with others securely (without making the entire bucket public), you generate a Signed URL that:
    -> Grants temporary access (for download or upload).
    -> Is valid only for a specific time duration (e.g., 5 minutes, 1 hour, etc.).
    -> Can be restricted to specific actions (GET for download, PUT for upload).

    How it Works
        -> You (with AWS credentials) generate the signed URL.
        -> AWS takes the request info + your IAM permissions + expiration time → generates a special URL with a signature.
        -> You share this URL with someone.
        -> The recipient can access the file directly using just the URL (no AWS account needed).
        -> Once the expiry time is over, the link stops working.

    Use Cases
        -> Sharing private files (e.g., invoices, reports, media) with users.
        -> Allowing users to upload files directly to S3 (without exposing your AWS keys).
        -> Temporary access for testing/debugging.
        -> Download links for apps/websites without making objects public.

    Example Commands
        -> Generate a Pre-signed URL (Download)
            aws s3 presign s3://my-bucket-name/myfile.txt --expires-in 3600
        -> This creates a link valid for 1 hour.

## IAM

    What is IAM?
        -> IAM (Identity and Access Management) is AWS’s security system that controls who can access AWS and what they can do.

    Main Parts
        Users → Individual people/apps with login or access keys.
        Groups → Collection of users (e.g., Developers, Admins).
        Roles → Temporary permissions for AWS services (no passwords/keys).
        Policies → Rules written in JSON that say Allow/Deny actions (like "Can read S3 buckets").

    Why Important?
        -> Protects your AWS account.
        -> Ensures least privilege (give only needed access).
        -> Supports MFA, temporary credentials, and cross-account access.

## ⚡ What is a Group in IAM?

    -> An IAM Group is just a collection of IAM users.
    -> It doesn’t have its own credentials (no password, no access keys).
    -> It exists only to make permission management easier.
    -> Policies are attached to groups → every user in that group inherits the same permissions.

    ⚡ Why Use Groups?
    -> Imagine you have 50 developers.
    -> Instead of attaching the same "DeveloperPolicy" to each user,
        -> Create a Developers Group, attach the policy once, and just add users to that group.
        -> This makes management easier, consistent, and less error-prone.

    ⚡ Key Points About Groups:
        Only Users, No Nesting
            -> A group can contain multiple users.
            -> A user can be in multiple groups.
            -> But groups cannot contain other groups.

        Policy Attachment
            -> You attach policies (JSON rules) to groups.
            -> Every user in that group inherits those policies automatically.

        Best Practice
            -> Create groups for job roles (e.g., Admins, Developers, ReadOnlyUsers).
            -> Attach permissions at group level, not user level, for easier management.

        Default Limit
            -> A user can belong to up to 10 groups.
            -> Each group can have up to 5,000 users (can be increased via AWS Support).

    ⚡ Example
        Scenario:
            -> You have 3 users: Alice, Bob, Charlie.
            -> You want them to only read S3 buckets.

        Steps:
            -> Create a group → S3ReadOnlyGroup.
            -> Attach AmazonS3ReadOnlyAccess policy to the group.
            -> Add Alice, Bob, Charlie to the group.

        -> Now all 3 can only read S3 without writing any extra policy for each.

## IAM Policies

    ⚡ What They Are
        -> JSON documents that define permissions in AWS.
        -> Decide: Who can do what, on which resource, and under which conditions.

    ⚡ Structure
        -> Version → Always "2012-10-17".
        -> Statement → Rule block(s).
        -> Effect: Allow or Deny.
        -> Action: The AWS actions (s3:PutObject, ec2:StartInstances, etc.).
        -> Resource: Specific AWS resource (using ARN).
        -> Condition (optional): Extra restrictions (IP, time, MFA, etc.).

    ⚡ Types of Policies
        -> AWS Managed → Prebuilt by AWS (e.g., AdministratorAccess).
        -> Customer Managed → You create and manage them.
        -> Inline → Attached directly to a single user, group, or role.

    ⚡ Where They Apply
        -> Identity-based → Attached to users, groups, or roles.
        -> Resource-based → Attached to resources (e.g., S3 bucket policy).
        -> Permission boundaries → Limit max permissions for a role/user.
        -> SCP (Service Control Policies) → Used in AWS Organizations to set account-wide limits.
        -> Session policies → Temporary, applied when assuming a role.

    ⚡ Evaluation Logic
        -> Start with default deny.
        -> If an explicit deny exists → ❌ Always denied.
        -> If an explicit allow exists (and no deny) → ⚡ Allowed.
        -> Priority: Explicit Deny > Allow > Default Deny

    ⚡ Examples
        -> Allow only read access to S3.
        -> Deny termination of EC2 instances.
        -> Allow S3 upload only from a specific IP.

    ⚡ Best Practices
        -> Use groups with policies, not direct user attachments.
        -> Apply least privilege (only what’s required).
        -> Use AWS managed policies for common tasks, customer managed for custom needs.
        -> Regularly review policies with IAM Access Analyzer.

## ⚡ IAM Roles in AWS

    ⚡ What is a Role?
        -> A temporary identity in AWS with permissions defined by policies.
        -> Unlike users, roles don’t have long-term credentials (passwords or access keys).
        -> Instead, they are assumed by:
            -> AWS services (EC2, Lambda, etc.),
            -> Users in your AWS account,
            -> Users/federated identities from other AWS accounts.

    ⚡ Key Points
        -> Trust Policy → Defines who can assume the role (e.g., EC2, another AWS account, SSO user).
        -> Permission Policy → Defines what the role can do (e.g., S3 full access, DynamoDB read).
        -> Uses temporary security credentials (STS tokens) that expire after some time.

    ⚡ Example Use Cases
        -> EC2 Instance Role → Instead of hardcoding AWS keys, attach a role to EC2 so it can access S3.
        -> Lambda Role → Lambda function needs permissions to write logs to CloudWatch.
        -> Cross-Account Access → Allow account A to assume a role in account B.

    ⚡ Flow (How it Works)
        -> Entity requests to assume role (EC2, user, AWS service, etc.).
        -> AWS verifies trust policy (is this entity allowed?).
        -> AWS issues temporary credentials (access key, secret key, session token).
        -> The entity uses those credentials until they expire.

    ⚡ Best Practices
        -> Always use roles instead of embedding long-term AWS credentials in code.
        -> Follow least privilege principle → Give only necessary permissions.
        -> For services (EC2, Lambda, ECS) → assign IAM Roles directly instead of passing keys.

## AWS SDK in Node.js

    The AWS SDK for Node.js is a toolkit that lets your app directly use AWS services (like S3, DynamoDB, EC2, etc.) without writing raw API calls.

    ✅ Handles authentication & request signing
    ✅ Provides easy methods (putObject, getObject, etc.)
    ✅ Works with async/await & supports all AWS services

## 🔥 What the Presigner Is ?

    -> A presigner generates temporary, signed URLs for S3 operations (GET, PUT, DELETE, etc.).
    -> These URLs contain an AWS Signature (SigV4) so a client can call S3 directly without AWS credentials, only for a limited time.

    How it works (high level)
        -> You run code on a server that has AWS credentials (or an assumed role).
        -> The presigner builds the canonical request for the S3 operation and creates the StringToSign.
        -> It derives the SigV4 signing key from your secret key (and date/region/service) and HMACs the StringToSign → signature.
        -> It returns a URL with query parameters such as:
            X-Amz-Algorithm, X-Amz-Credential, X-Amz-Date, X-Amz-Expires, X-Amz-SignedHeaders, X-Amz-Signature
        -> Anyone with the URL can perform the operation until the expiry time.

    📦 Common uses & types
        GET URL — download a private object.
        PUT URL — allow direct upload (client uploads via PUT).
        DELETE URL — delete object without credentials.

## What is a CDN?

    -> A Content Delivery Network (CDN) is a globally distributed system of servers (called edge locations) that deliver internet content (HTML, CSS, JS, images, videos, APIs, files) to users based on their geographic location.
    -> Instead of serving content directly from your origin server (like an AWS S3 bucket or EC2 instance), a CDN caches and delivers it from the closest edge server to the user.

    How Does a CDN Work?
        User Requests Content
        -> A user in New York requests your website (example: image.png).

        DNS Resolution to Nearest Edge Server
        -> The request is routed to the nearest CDN edge location (maybe in New York).

        Check Cache
        -> If the edge location has the content cached → It serves immediately (cache hit).
        -> If not cached (cache miss) → It fetches the content from the origin server (S3 bucket, EC2, or custom server), stores it at the edge, and serves the user.

        Future Requests
        -> Any other users near New York get the cached copy instantly.

    Benefits of CDN
        Low Latency → Content loads faster since it’s delivered from the nearest edge.
        Scalability → Can handle sudden traffic spikes (thousands/millions of requests).
        Reliability → If one edge server goes down, traffic reroutes to the next closest.
        Security → Many CDNs (like AWS CloudFront) integrate with WAF, SSL, DDoS protection.
        Reduced Costs → Fewer direct hits to the origin (S3/EC2), saving bandwidth and request charges.

## Cache Invalidation

    -> In AWS CloudFront, cache invalidation refers to the process of removing specific objects (files) from the CloudFront edge caches before they expire.

    Why it exists:
        -> CloudFront caches your content (HTML, CSS, JS, images, etc.) at edge locations for faster delivery.
        -> Each cached object has a Time-to-Live (TTL) that you set in the response headers (Cache-Control / Expires) or in CloudFront behaviors.
        -> But sometimes you update a file (say style.css) on your origin (S3, EC2, etc.), and CloudFront still serves the old cached version until TTL expires.
        -> To force CloudFront to fetch the new version, you use invalidation.

    How Invalidation Works
        -> You create an invalidation request in CloudFront.
        -> You specify the path(s) to invalidate, e.g.
            /index.html → clears just the homepage
            /images/* → clears everything under /images/
            /* → clears the entire distribution (more expensive)
        -> After invalidation, the next request to that object will go back to your origin, fetch the latest version, and cache it again.

    Cost
        -> The first 1,000 paths per month are free.
        -> After that, you pay per invalidation path (unless you use /*, which counts as 1 path regardless of number of files).
