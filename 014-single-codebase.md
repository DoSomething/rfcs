# Single Codebase

We should consolidate our services into a single repository, with a consolidated monolithic backend.

**Discussion:** https://github.com/DoSomething/rfcs/pull/14

## Problem

We rebuilt our old [Drupal 7 monolith](https://github.com/dosomething/website-2013) into a suite of services – [Northstar](https://github.com/DoSomething/northstar), [Rogue](https://github.com/DoSomething/rogue), [Phoenix](https://github.com/DoSomething/phoenix-next), etc. – between 2013-2019. We avoided [the quagmire of a total rewrite](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/) by extracting functionality [piece-by-piece](https://martinfowler.com/bliki/StranglerFigApplication.html) & have been generally happy with what we've built since then.

However, breaking our monolith into a service-oriented architecture introduced new issues of its own:

- **Blurred boundaries:** The [bounded contexts](https://martinfowler.com/bliki/BoundedContext.html) for each of our services (especially our identity & activity APIs) can be blurry. For example, is joining a club part of a user's identity or an action that they take? We're often forced to make this decision early in a feature's development process, before the technical work has fully taken shape.

- **Technical overhead:** Logic that straddles multiple domains is also inevitably more complicated, requiring network requests for what could otherwise be a function call or `JOIN` statement. It makes traditionally simple logic into distributed systems problems where we have to decide whether we'll trade consistency for performance or vice versa.

- **Testing limitations:** We know we get the best bang for our buck from [high-level integration tests](https://github.com/DoSomething/rfcs/blob/master/004-testing.md), but separating into services forces us to use mocking extensively because the majority of our application (e.g. users in Rogue, or users & posts in Phoenix) is always an external dependency in any given test! This places a large burden on our intra-service contracts.

- **Knowledge silos:** Finally, this separation reinforces silos (e.g. "you have to talk to Dave about that app" or "that's X application's responsibility, not ours"). We have a smaller team today than when we first started splitting out into a service oriented architecture, and it's important that we can all understand & comfortably work on the whole system.



## Proposal

We should consolidate our backend services (Northstar, Rogue, and Chompy) into **a single monolithic Laravel application.**

- This will **simplify architectural decisions** & enable us to iterate faster. Instead of sorting new functionality into strict bounded contexts up front, we'll be able to simply place new entities into a single backend context. Architectural decisions will be less binding, as it's easier to refactor within a single codebase than across network boundaries.

- It will also **simplify adding behavior that straddles contexts**, like clubs, by replacing network requests with function calls. We'll also be able to ensure better consistency by using more transactional validation.

- Keeping more of our business entities in the same domain allows **easier integration testing**, since we can seed (and otherwise directly reference) data that would currently live across API boundaries, like user IDs & clubs.

- We have less applications to keep up-to-date with security & framework upgrades!

For services that may not make sense to fold into the monolith, like [GraphQL](https://github.com/DoSomething/graphql/) or [Gambit](https://github.com/DoSomething/gambit/), we should still collaborate on them [**as part of a single monorepo**](https://docs.google.com/document/d/1a3e0Wpv9qeRscspdbN4wVR4Chz5oJFb9HH69x2pngSg/edit), side-by-side with our consolidated backend application. This allows us to retain the technical benefits we've gotten from separating out these services, while collaborating in one place.



## Drawbacks/Alternatives

#### Okay, but on the flip side – why _did_ we move from a monolith to (micro)services?

Our Drupal application had a lot of intertwined modules that could be hard to follow, due to Drupal 6's "hooks" based architecture. We wanted to use service boundaries to force a "separation of concerns" that we thought would keep our codebase tidier.

At the time, microservices were the architecture du jour & we followed the success stories of teams many times our size, from Spotify to Google, that promised that smaller services would increase velocity and allow teams to ship faster. At first, we had roughly one team of developers per application ("Rocket" for Phoenix & Northstar, "Bleed" for Rogue, and "O'Doyle" for Gambit & Blink) and it really did increase velocity! (That velocity slowed as a we began to build more cross-cutting features later on.)

We also found that splitting our monolithic application into services allowed us to choose "the best tool for the job", such as using Node.js for our chatbot. (We love PHP at Do Something, but we know it's not appropriate for every task).

Finally, separating logic into individual services allowed us to scale them more granularly – for example, spinning up our SMS service during a broadcast, but not our front-end website. (This became less effective over time, as we now scale up Gambit, Northstar, and Rogue in tandem every time we run a broadcast.) 

I don't think these were the wrong choices at the time, especially given the information we had! Given what we've learned over the past five years, though, I think we would not make the same decision if we started down this path today.


#### Why would we not want to make this change now?

First of all, it takes time! We'd be betting that the time we'd take to rearchitect these applications now would be outweighed by the increase in velocity we'd see as a result. Luckily, we're in a bit of a "lull" at the end of the year & this feels like an opportune time to "reset" and set ourselves up for success in the new year.

We might also find that if we were to grow the team aggressively in the future, we'd once again start tripping over each other in a single codebase. It's hard to predict the future, but it doesn't seem likely at the moment that will be the case.



## Additional Resources

A lot of inspiration for this proposal comes from DHH's [The Majestic Monolith](https://m.signalvnoise.com/the-majestic-monolith/) (2015) and [The Majestic Monolith Can Become The Citadel](https://m.signalvnoise.com/the-majestic-monolith-can-become-the-citadel/) (2020), which detail Basecamp's "majestic" monolithic architecture. They have a small team of 12 developers, and most of their functionality lives in a single codebase (with a few extracted services where necessary).

Another great case study is Segment's [Goodbye Microservices](https://segment.com/blog/goodbye-microservices/) (2018), which explains why they transitioned their famous microservice architecture back to a monolith. They cite improved productivity, testability, and reduced operational overhead.

Finally, in [MonolithFirst](https://martinfowler.com/bliki/MonolithFirst.html) (2015), Martin Fowler argues that projects should start as a monolith and only be broken up once the system's bounded contexts are solidly established. At that point, microservices can be peeled off around the edges.
