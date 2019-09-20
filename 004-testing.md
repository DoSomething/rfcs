# Testing

We write automated tests to help us catch mistakes before they impact our users. Automated tests also allow us to test more thoroughly and quickly than if we were testing "by hand", which lets us iterate faster & more confidently!

**Discussion:** https://github.com/DoSomething/rfcs/pull/4

## Problem

We've found that when we skip writing tests, we're less confident in changes we make. That eventually slows down feature development and discourages refactoring. On the other hand, we don't want to spend all of our time writing tests, since we recognize that they're just a tool (and don't directly provide value to our users).

## Proposal

Well, we should write tests, of course! Here's our current "best practices" for testing:

### Philosophy
There are lots of opinions on how to write tests! We try be pragmatic.
We write tests to save ourselves from doing tedious QA work, and our tests reflect that: they're the steps that an engineer or PM would use to test a given function, component, API endpoint, or user flow... but translated into a set of instructions so a machine can do it for us!

We [don't practice strict TDD](https://dhh.dk/2014/tdd-is-dead-long-live-testing.html) or pursue 100% test coverage. Instead, we try to get the most "bang for our buck" by writing [fewer higher-level integration tests](https://youtu.be/Fha2bVoC8SE) that test the real functionality that our application exposes, rather than isolated pieces.

In [Phoenix](https://github.com/DoSomething/phoenix), this means the majority of our test coverage comes from [Cypress](https://www.cypress.io/) browser tests, which step through full application flows just like a real user. We also maintain some unit tests, written with [Jest](https://jestjs.io/) & [Enzyme](https://airbnb.io/enzyme/), for more complicated components. In backend Laravel applications, like [Northstar](https://github.com/DoSomething/northstar) and [Rogue](https://github.com/DoSomething/rogue), the majority of our test coverage comes from [PHPUnit](https://laravel.com/docs/5.5/http-tests) functional tests, which simulate real API requests.

### Test Structure

In general, all of our tests follow the same basic structure: "given this scenario, when I do X, I see Y as a result". For example,  ["given this JSON payload, the ContentfulEntry renders the appropriate component"](https://github.com/DoSomething/phoenix-next/blob/dc9713b568060a3a459c715ffb97854321d4ae10/resources/assets/components/ContentfulEntry/ContentfulEntry.test.js#L19-L26) or ["given a user isn't signed up for the campaign, if they click on the Join Now button, then they should see the affirmation modal"](https://github.com/DoSomething/phoenix-next/blob/dc9713b568060a3a459c715ffb97854321d4ae10/cypress/integration/campaign-signup.js#L35-L53).

```js
describe('Test Suite name', () => {
   it('should do the things', () => {
      // 1. Given some state, such as from a mock or factory...
      // 2. When something happens (click, HTTP request, function call...)
      // 3. Assert the result matches our expectations!
   }
});
```

### Process
We like to write tests while adding features or fixing bugs, because writing tests help us "check our work" & demonstrate functionality in pull requests, and because otherwise it's easy to forget! We're still catching up on tests we skipped writing when we originally built Phoenix, Chompy, and other applications.

We try not to merge in code without tests, and never merge in a pull request that introduces a test failure.

### Mocking

Since each of our applications are only small piece of our overall system, we use [mocks](https://en.wikipedia.org/wiki/Mock_object) to avoid relying on external dependencies in our tests. This helps to save us from "flakiness" (such as network issues) and allows us to easily simulate how applications would react to hypothetical scenarios (such as quickly testing how Phoenix's campaign landing page looks for a user with or without an existing campaign signup in Rogue).

By mocking external dependencies, we also ensure a change in one application doesn't break the test suite in another app. Instead, each application's test suite ensures its "contract" with other apps is respected.

## Drawbacks/Alternatives

Writing tests takes time that could otherwise be spent on ~churning out code~ feature development. We want to be mindful that we're not spending more time on writing tests than the value (less bugs! less time spent manually testing! peace of mind!) they provide.
