
# Resource Naming

We should consistently name our resources (applications, databases, URLs, etc).

**Discussion:** https://github.com/DoSomething/rfcs/pull/2

## Problem

We [historically had some issues with naming applications](https://github.com/DoSomething/internal/issues/366). For example, some applications would be prefixed with their environment and others would be suffixed, and we used "staging" in some cases and "QA" in others.

This was confusing because you had to remember how each individual application handled naming.

## Proposal

Environments should be consistent between applications. Most applications should have:

- **Local** apps exist on engineer’s computers, and are used while working on new features or bug fixes. When they need something from another application, they’ll use our development environment. These either run on `localhost`, or using a `{service}.test` hostname.
- **Development** apps are bare-bones versions of applications that developers connect to when building features. They are populated with fake data & routinely reset to a “factory default” state. This is always running the master branch. You can find a service’s development environment at `{service}-dev.dosomething.org`.
- **QA** apps (once referred to as "Thor") are the last step before production. They are refreshed with a copy of production data weekly. This is where we do QA testing before code goes live. This is always running the master branch. You can find a service’s QA environment at `{service}-qa.dosomething.org`.
- **Production** apps are “live”! This is the website that our users see.

We should also name resources (applications, databases, etc.) consistently:

- Names should always begin with a `dosomething-` prefix to avoid global namespace conflicts.
- Applications should be named consistently with their repository, e.g. `dosomething-northstar` for [Northstar](https://github.com/DoSomething/northstar).
- Applications should be suffixed with the environment name if not in production, e.g. `dosomething-northstar-qa` or `dosomething-northstar-dev`.

Finally, URLs should be named for the application's domain (so that it's more memorable and accessible to non-engineers). For example, Northstar should live at `identity.dosomething.org` because it's our identity service.

## Drawbacks/Alternatives

This involved an upfront time investment to migrate inconsistently named resources to match the standardized naming scheme. (And it's one that we still haven't finished over a year later! See [#165498590](https://www.pivotaltracker.com/story/show/165498590) and [#165498648](https://www.pivotaltracker.com/story/show/165498648)).
