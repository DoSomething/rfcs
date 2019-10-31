# GraphQL Pagination

We should use [Relay Cursor Connections](https://facebook.github.io/relay/graphql/connections.htm#) to paginate results in GraphQL. 

**Discussion:** https://github.com/DoSomething/rfcs/pull/6

## Problem

We currently return plain lists, like `[Post]`, for paginated GraphQL queries:

```gql
{
  posts(count: 10, page: $page) {
    id
    type
    url
  }
}
```

This gives us no way to know whether there's another page of results without making a request to check. Because we built these on top of standard `OFFSET` pagination, there are also some performance issues with larger datasets & the potential for duplicate results to appear (described in more detail under "Alternatives").

## Proposal

We should use [Relay Cursor Connections](https://facebook.github.io/relay/graphql/connections.htm#) to paginate results in GraphQL, and add REST API support to paginate via cursors (e.g. "get me all the results after #1015" rather than "get me the tenth page of results") to Northstar and Rogue.

```gql
{
  paginatedPosts(first: 10, after: $cursor) {
    edges {
      cursor
      node {
        id
        type
        url
      }
    }
    pageInfo {
      hasNextPage
    }
  }
}
```

This relies on generating "opaque" cursors for each element in the results. The cursor, e.g. `Nzk1MS4yNDA3Nw==` encodes information about where the item appears in the results (the ID & the sorted column value, if applicable).

## Drawbacks

This is certainly more verbose than using standard lists! I could see a good argument for having both types of queries available (say, `campaigns` and `paginatedCampaigns`) so we can use each where it makes the most sense.

Using cursors for pagination also requires some REST API updates (to generate the cursor for each item in a paginated response and then to build the database query based on the given `?cursor` query string).

## Alternatives

There are [multiple approaches to pagination with GraphQL](https://www.apollographql.com/docs/react/data/pagination/).

The simplest option is to return a plain list, e.g. `[Post]`, but this has a big downside – we have no way of knowing when we've reached the end of the result set (until we make a query for the next page and get zero results). This is why campaign galleries always show "Load More" even when they don't have more pages to load:

```gql
posts(…args): [Post]
```

We could build a home-grown solution that adds `hasNextPage` and `hasPreviousPage` fields:

```gql
posts(page: Int, ...): {
  hasNextPage: Bool!
  hasPreviousPage: Bool!
  results: [Post]
}
```

This solves our main issue (not knowing when we've reached the end of our result set), but still has some downsides because we're using traditional numbered pages:
- First, using `?page=N` gets progressively slower on large collections, because the database has to iterate over N pages of results before "slicing" out the new page. We've run into this problem when Quasar scrapes Northstar's `v2/users` endpoint (and responses get slower and slower as it pages through results).
- Second, we can accidentally skip over data! If we're sorting so that new items are shown first (like our campaign galleries), users will see duplicates if paginating when new results are added. (Since if we offset over the first 10 posts but then a new one is added, we'll see that 10th post again on the second page).

Neither of those are deal-breakers, but using cursors handily solves them for us! We also get the benefit of following a specification that has worked well for other folks, like Facebook and [GitHub](https://developer.github.com/v4/), rather than blazing our own path.

## References

- [GraphQL Documentation: Pagination](https://graphql.org/learn/pagination/)
- [Shopify Engineering: "Pagination with Relative Cursors"](https://engineering.shopify.com/blogs/engineering/pagination-relative-cursors)
- [Slack Engineering: Evolving API Pagination at Slack](https://slack.engineering/evolving-api-pagination-at-slack-1c1f644f8e12)

