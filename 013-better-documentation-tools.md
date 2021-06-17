# Use Better Documentation Tools

There are a few different documentation tools that would help us better document our projects that can be both maintained in the code repository and hosted on the same domain as the project.

**Discussion:** <https://github.com/DoSomething/rfcs/pull/13>

## Problem

Documentation is more readily maintained and kept up-to-date when it is easy to work with and update.

[GitBooks](https://www.gitbook.com/) has served us well enough for now, but there have been a number of pain points with their updated offerings.

When we initially had implemented GitBooks as our documentation tool, it was what is now considered their [Legacy](https://legacy.gitbook.com/) offering. It was great for project docs because they had an NPM package you could run locally to build the docs and visualize them on your local computer to see how it would ultimately look and how your edits were rendered. This means it would also easily highlight when you had errors in your markdown, like missing parenthesis in a text link, or missing pages from the summary, etc.

With GitBook's latest version, there is no NPM package to help build and visualize the docs you are writing. To visualize them, you need to push up a specifically named branch, and push any edits to this branch.

This process also works a bit _funky_ because you cannot always easily delete the branches on GitBooks after they are merged. The reason for this is that technically the branches on GitBooks are not meant to be used for previewing your doc edits, but only for versioning your docs.

As a result, most devs who do add edits to the docs where we use GitBooks, hope for the best when the code is merged to the main branch and the GitBook docs update.

We also do not have the GitBook docs within our domain and instead host them through the GitBooks domain, which makes for a very disjointed experience (and difficult to remember URL).

GitBooks user interface tools are quite good, but it only really benefits when the documentation is maintained using the GitBooks UI on their domain. We can still use GitBooks for documenation outside our code repositories but I propose we move to using another tool for our code documentation.

## Proposal

There are a few different documentation tools that would help us better document our projects that can be both maintained in the code repository and hosted on the same domain as the project. I experimented with a couple that seemed to be best suited for DoSomething's tech project needs.

One of the core criteria I was looking for, is a tool that lets us keep the documentation in the same codebase as the project and could be hosted on the same domain. 

Ideally, it would also reflect the respective environment: 

- `https://activity-dev.dosomething.org/docs`
- `https://activity-qa.dosomething.org/docs`
- `https://activity.dosomething.org/docs`

With the above environments, changes in the documentation describing new features, would also match the environment where the feature is available (if documentation is closedly associated with the pull request adding the feature).

This would allow developers reviewing updates to the documentation to easily _see_ the updates in review app builds, rendered as they would be once the code is merged.

Another criteria was that the tool can be easily integrated with a good search solution like Algolia Search.

---

_The following are some of the tools I reviewed with associated pull requests showing implementation examples with the tool on Rogue:_

### Larecipe

A documentation tool specific to Laravel projects, [**Larecipe**](https://larecipe.binarytorch.com.my/) was one of the easiest to get setup and start writing documentation.

There is a [prototype pull request](https://github.com/DoSomething/rogue/pull/1110) showing the setup and converted docs, and a link to the [hosted documentation](https://dosomething-rogue-dev-pr-1110.herokuapp.com/docs/1.0/development/overview) in the review app.

#### Installation process:

Install the composer package:

```shell
$ composer require binarytorch/larecipe
```

Publish the package assets:

```shell
$ php artisan larecipe:install
```

Settings can be configured in the `config/larecipe.php` file.

#### Pros

- Dead simple. Like _suuuuuper_ simple to setup and work with (I had it set up in about a minute and could immediately start to move markdown content from `/docs` to the designated directory in `/resources/docs`).
- Great for hosting docs as a route within domain `https://activity.dosomething.org/docs`.
- [Supports search](https://larecipe.binarytorch.com.my/docs/2.2/search) with Algolia.
- Supports easy versioning of documentation.
- Maybe a pro, it supports VueJS components which has become closely associated with Laravel, but we use React in most of our projects. 🤷‍♂️
- Allows for easy Authentication gated routing of docs if necessary.

#### Cons

- Laravel framework only; would not really work for other projects not using this framework.
- Not great for hosting docs using GitHub Pages (but also not really meant for that).

### Docusaurus

A documentation tool developed by Facebook, [**Docusaurus**](https://v2.docusaurus.io/) was a bit more involved to get setup and configured, but I think its the most likely candidate for us to implement across our repositories since it is an NPM package and easily customizeable.

It can be setup as a GitHub Pages deployed documentation or hosted by the domain.

There is a [prototype pull request](https://github.com/DoSomething/rogue/pull/1111) showing the setup and converted docs, and a link to the [hosted documentation](http://dosomething-rogue-dev-pr-1111.herokuapp.com/build/docs/index.html) in the review app.

#### Installation

Used `npx` to initialize the setup without needing to download the `init` package:

```shell
$ npx @docusaurus/init@next init [name] [template]
```

I specifically ran it within the Rogue root directory as:

```shell
$ npx @docusaurus/init@next init docusaurus classic
```

This will create a `docusaurus/` directory with associated files (it can be named whatever we want, it does not have to be "docusaurus").

You can also run different scripts to serve a local copy of the docs for development, or for building the docs for production and more.

#### Pros

- [Supports search](https://v2.docusaurus.io/docs/search) with Algolia.
- [Supports MDX](https://v2.docusaurus.io/docs/markdown-features/#embedding-react-components-with-mdx) which is Markdown that allows for embedding React components, either custom components we use to spice up our documentation or potentially could use as a display of example components that exist in system (although Storybook may be better for this; see "Alternative" section below).
- Versatility allows for setup and hosting within domain (like I did in PR) or also easily publish docs to GitHub Pages.


#### Cons

- Not necessarily a con, but v2 is currently in alpha, although it is a pretty stable alpha (_I set up the example pull request using v2 because it made more sense to me than trying to set it up using v1 and if we roll with it, then needing to migrate at a future point when v1 is no longer supported_).
- Took some trial and error to get things setup; the `docusaurus/` directory contains its own `package.json` and scripts. It took some messing around to get it working as expected along with setups for our continuous integration and Heroku review apps (but once setup, it should hoepfully not need any additional support).

## Considerations

Ultimately, I think we would probably want to use the same documentation tool across our code repositories so that there is less to be confused about or figure out when switching between projects. Therefore, I think **Docusaurus** is probably the best option for us to try out among DoSomething projects.

## Drawbacks

The only real drawback is needing to put in the work to implement a new tool (although my prototype pull requests do some of that for us, if we use one of those tools), and shifting the docs over into the new system. Luckily most of our docs are in markdown and these tools support markdown, so it will be mostly just a copy/paste task with a bit of cleanup.

## Alternatives

[**Docsify**](https://docsify.js.org/) looks like an easy to use tool to get setup, but I was concerned that it did not have the best options for search.

[**Storybook**](https://storybook.js.org/) seems like a great solution for documenting our React UI components, but not necessarily as a general documentation tool. On projects with React components, I think it would work great _as a compliment to_ one of the other documentation tools suggested.
