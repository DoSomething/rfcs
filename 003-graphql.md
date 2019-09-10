# GraphQL

We should use GraphQL for all data-fetching in the future.

**Discussion:** https://github.com/DoSomething/rfcs/pull/3

## Problem(s)

There were a couple of problems we looked to GraphQL to help solve:

 - We reimplemented cross-application includes (such as `?include=user` on a list of posts in Rogue) in multiple apps. This caused different backend services to have to act as a "gateway" depending on where a request started.
 - We found that we would eventually implement the same batching & caching logic in each application (for example, fetching campaigns [in Rogue](https://git.io/fjjGZ) or users [in our now-defunct competitions app](https://github.com/DoSomething/gladiator/blob/534ca9834a5369ededaed56deff33404bbc94e99/app/Repositories/DatabaseUserRepository.php#L62-L163)). In each case, we'd have to reconsider the right order-of-operations and maximum batch size for good performance.
 - Because each application maintained it's own caches, it was hard to maintain a consistent cache policy. (For example, one application might cache users for an hour while another didn't cache them at all).
 - Finally, we had to do a lot of busy-work to [normalize our Redux state](https://redux.js.org/recipes/structuring-reducers/normalizing-state-shape) in Phoenix, which slowed down development of new features. (See [DoSomething/phoenix-next#767](https://github.com/DoSomething/phoenix-next/pull/767) and [DoSomething/phoenix-next#1123](https://github.com/DoSomething/phoenix-next/pull/1123)).

## Proposal

We built our GraphQL gateway (see [graphql.dosomething.org](https://graphql.dosomething.org/)) in November 2017. We have gradually added more resources (such as Contentful spaces and embed metadata) and applications (such as Gambit) over time.

This gives us a single [universal schema](https://principledgraphql.com/integrity#1-one-graph) (so developers don't need to worry about which application holds which data). It lets us optimally [batch requests to backend services](https://www.apollographql.com/docs/graphql-tools/connectors/#dataloader-and-caching) and acts as a centralized "wrapper" around third-party APIs like [Contentful](https://www.contentful.com/).

It also allows us to organically evolve our API over time (since we can add fields without bloating existing requests that don't need them, and [deprecate old fields](https://www.apollographql.com/docs/graphql-tools/generate-schema/#descriptions--deprecations) without breaking existing clients). We also get fantastic developer tools, like the [Apollo Chrome extension](https://chrome.google.com/webstore/detail/apollo-client-developer-t/jdkknkkbebbapilgoeccciglkfbmbnfm?hl=en-US) and [Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=apollographql.vscode-apollo).

We should continue to roll out GraphQL and reduce our reliance on custom data-fetching logic!

## Drawbacks/Alternatives

Using GraphQL relies on more tooling than traditional REST HTTP requests (since you'd likely want a purpose-built GraphQL client). It also means we're investing in a particular technical stack that may not always be as popular [as it currently is](https://www.apollographql.com/docs/intro/benefits/#ready-for-production) (whereas REST isn't going anywhere).

We also considered [JSON:API](https://jsonapi.org/), which is popular in the Ember.js world & solves similar issues.

## Additional Resources

- [Principled GraphQL](https://principledgraphql.com/)
- [Apollo Docs: Why GraphQL?](https://www.apollographql.com/docs/intro/benefits/)
- [Artsy: Is GraphQL the future?](https://artsy.github.io/blog/2018/05/08/is-graphql-the-future/)
