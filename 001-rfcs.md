
# RFCs

We want to start using RFCs to discuss (and document) architectural decisions.

**Discussion:** https://github.com/DoSomething/rfcs/pull/1

## Problem

Ideas tend to get stuck in one person's head by default (or lost in 1:1 conversations). As our team grows, we want to make it easy for everyone to take part in architectural/process discussions & see ideas we've committed to trying.

## Proposal

We should adopt a simple RFC process, inspired by the process used by the engineering teams at [Artsy](https://artsy.github.io/blog/2019/04/11/on-an-rfcs-process/) and [GOV.UK](https://github.com/alphagov/govuk-rfcs) and on larger open-source projects like [React](https://github.com/reactjs/rfcs) or [Rust](https://github.com/rust-lang/rfcs). (And while their namesake comes from the [exhaustive IETF standards documents](https://www.rfc-editor.org/rfc-index.html), we definetely don't want to replicate that!)

By documenting architectural goals in a semi-formal manner like this, we hope to increase transparency (since a public document is by definition a lot more transparent than one-one-one discussions). We're also hoping this reduces friction by helping to spread ideas _before_ the pull request review process.

I'd recommend we start with a [very lightweight process](https://github.com/DoSomething/rfcs/tree/hello-rfcs#process) and introduce more rules over time.

## Drawbacks/Alternatives

Two possible downsides to this process are the creation of additional "busywork" (talking about doing the work, rather than "just doing it") and potentially reinforcing that the "loudest voice wins" (see [this section of Artsy's blog post](https://artsy.github.io/blog/2019/04/11/on-an-rfcs-process/#What.are.the.alternatives.)). Personally, I think in both cases we're better off with _some_ process than none though!

We could also use our existing [opportunity assessment](https://docs.google.com/document/d/1KCl9MadAftdNxPYx_zuuWTYW25lgyqZkAM2vSXVH6T4/edit) template for discussions like this (and have done so in the past)! However, I think that process is better suited for product ideas than ongoing architectural approaches.

