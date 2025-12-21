# Blue Green Deployment Strategy

![alt text](/Assets/image.png)

## What it is in simple words
- Blue Green deployment is a way to update an application without downtime.

- You keep two identical environments:
    - Blue: the current live version that users are using
    - Green: the new version with the latest changes

- Users are always connected to only one environment.
- When the new version is ready and tested on Green, you switch traffic from Blue to Green instantly.
- If something goes wrong, you switch back to Blue just as fast.
- If everything is stable you upgrade Blue to lastest version for future tests and this life cycle continues.

## How it works step by step
- Blue environment is live and serving users
- You deploy the new version to Green
- You test Green fully without users noticing
- You switch traffic from Blue to Green
- Green becomes live
- Blue stays idle as a backup
- It there is no conflict/issue on Green, You update Blue for future testings.

## Pros
- **Zero downtime**: Users do not experience any service interruption
- **Instant rollback**: If the deployment fails, switch back immediately
- **Safe deployments**: New version is tested in a production like setup
- **Simple concept**: Easy to understand and operate compared to complex strategies

## Cons
- **Higher cost**: You need two full environments running
- **Database challenges**: Hard when database schema changes are not backward compatible
- **Not ideal for stateful apps**: Apps that store in memory sessions can face issues
- **Traffic switch risks**: A wrong configuration can route traffic incorrectly

# Canary Deployment Strategy

![alt text](/Assets/image-2.png)

## What it is in simple words

- Canary deployment is a way to release a new version of your application to a small group of users first, instead of everyone.
- If everything works fine, you slowly increase the number of users who get the new version.
- If something goes wrong, you stop the rollout and keep most users safe.
- The name comes from the old practice of using canary birds in coal mines. If the canary was fine, it meant the mine was safe.

## How it works step by step

- Version v1 is running and serving all users
- Deploy version v2 alongside v1
- Send a small percentage of traffic to v2, for example 5 percent
- Monitor errors, latency, logs, and metrics
- If healthy, increase traffic to 25 percent, then 50 percent
- Finally, send 100 percent traffic to v2
- Remove v1

## Pros
- **Lower risk deployments**: Only a small set of users are affected if something breaks
- **Real user testing**: You test the new version with real traffic
- **Gradual rollout**: Issues are caught early before full impact
- **Better confidence**: You can observe performance and behavior in production

## Cons
- **More complex**: Requires monitoring, metrics, and traffic control
- **Harder rollback**: You must reduce traffic instead of a simple switch
- **Version compatibility issues**: Old and new versions must work together
- **Not instant**: Rollout takes time

# Rolling Deployment Strategy

![alt text](/Assets/rolling-deployment.gif)

## What it is in simple words

- Rolling deployment means updating your application one server at a time, instead of updating everything at once.
- While one server is being updated, the other servers continue serving users.
- This way, the application stays available during the deployment.
- No separate Blue or Green environment is needed.

## How it works step by step

- All servers are running the old version
- One server is taken out of traffic by the load balancer
- That server is updated to the new version
- Health checks pass
- The server is added back to traffic
- The process repeats for the next server
- Users never lose access to the application.

## How traffic is handled

- Load balancer temporarily removes one server
- Requests go to remaining healthy servers
- Updated server is added back after verification
- > Users → Load Balancer → Healthy servers only


## Pros

- Zero or near zero downtime
- Lower infrastructure cost compared to Blue Green
- No duplicate environment needed
- Simple to automate
- Works well with limited resources

## Cons

- Mixed versions running together
- Rollback is slow
- Risk if versions are not compatible
- Deployment takes longer
- Harder debugging when issues appear midway

# Recreate Deployment Strategy (Classic Stop; Then Start)

![alt text](/Assets/image-4.png)

## What it is in simple words

- Recreate deployment means:
    - Stop the old version completely
    - Deploy the new version
    - Start the application again
- During this time, the application is not available to users.
- There is intentional downtime.
- This is the oldest and simplest deployment strategy.

## How it works step by step

- All running servers or processes are stopped
- Users get errors or maintenance page
- New code is deployed
- Application is started
- Users can access the app again


## Why people still use it

- Because it is simple and predictable.
- No load balancer logic
- No traffic routing
- No version compatibility problems

## Pros

- Very simple
- No mixed versions
- Easy database migrations
- Low infrastructure cost
- Easy to understand and implement

## Cons

- Downtime is guaranteed
- Bad user experience
- Risky for production
- Manual rollback
- Not scalable


# Shadow Deployment (A/B style release)

![alt text](/Assets/image-5.png)

## What it is in simple words
- Shadow deployment is often confused with Canary or Blue Green, but it solves a different problem.
- Its main goal is testing new versions safely without affecting users.
- Shadow deployment means:
    - Users keep using the old stable version
    - The same user requests are copied and sent to a new version in the background
    - Users never see responses from the new version
    - You only observe logs, errors, performance, and behavior
- Think of it as running the new version in the shadow while the old one serves users.

## Simple flow

```
Users → Load Balancer → Old Version (live response)
                    └→ New Version (shadow, no response)
```
- The new version processes real production traffic but its output is ignored.

## How it works step by step

- Old version is serving all users
- New version is deployed alongside it
- Incoming requests are duplicated
- One copy goes to old version
- Another copy goes to new version
- Only old version response is returned
- Metrics and logs from new version are monitored
- No user impact at all.

## What Shadow deployment is used for

- Performance testing with real traffic
- Finding hidden bugs
- Testing scalability
- Validating new logic
- Dark launches of major features
- It is commonly used before Canary or Blue Green rollout.

## Pros

- Zero user impact
- Very safe
- Real production traffic
- Great for performance testing
- No rollback needed

## Cons

- Double infrastructure cost
- Hard to implement
- No functional validation(From user point-of-view)
- Data side effects risk
- Complex observability required

## Deployment Strategies Comparison

| Strategy | Downtime | Traffic Exposure | Rollback Speed | Infra Cost | Complexity | Risk Level | Best Use Case |
|--------|---------|------------------|---------------|-----------|------------|-----------|---------------|
| Recreate | Yes | 100% users affected | Manual and slow | Low | Very Low | High | Internal apps, low traffic, breaking DB changes |
| Rolling | Near zero | Gradual by servers | Slow | Low | Low | Medium | Small teams, limited infra, backward compatible apps |
| Blue Green | Zero | All users at once | Instant | High | Medium | Low | Production systems needing zero downtime |
| Canary | Zero | Small percentage first | Medium | Medium | High | Very Low | High traffic apps, reliability focused systems |
| Shadow | Zero | None (no user impact) | Not required | Very High | Very High | Very Low (infra), High (data) | Performance testing, risky changes |
