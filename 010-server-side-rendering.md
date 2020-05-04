# Server-Side Rendering

We should use [Next.js](https://nextjs.org) and [Heroku](https://www.heroku.com) to power server-side rendering for Phoenix.

**Discussion:** https://github.com/DoSomething/rfcs/pull/10

## Problem

Our front-end application, Phoenix, is not as scalable, simple, or performant as we want:

- Because Phoenix runs on traditional server processes, our **ability to scale is limited** (for authenticated users). If we’re unable to gracefully scale with traffic spikes, we might lose engagement at a key time.
- The application is split between a traditional backend server, written in [Laravel](https://laravel.com), and a client-side application, written in [React](https://reactjs.org). This **increases context switching for developers**, slowing down development.
- Because the application is almost completely rendered on the client, we see **reduced performance on slower devices** (like old phones). This may reduce engagement, since nobody likes waiting.
- Finally, we've been limited in what we can share on social networks (whose scrapers do not execute client-side code) since any social metadata must be fetched and rendered by a separate server-side process. This also impacts built-in web functionality, like using `#anchor-links` to link directly to a section of a page.

Despite these problems, we're generally happy with what we've built in Phoenix & don't want to consider a full re-write. We know that approach [often turns into a quagmire](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/) and so whatever we choose, we want to be able to adopt it incrementally with our existing codebase (sorry [Nuxt.js](https://nuxtjs.org), [Svelte](https://svelte.dev) and [Livewire](https://laravel-livewire.com)!)

## Proposal

We should use [Next.js](https://nextjs.org) and [Heroku](https://www.heroku.com) to power server-side rendering for Phoenix. They allow us to statically generate markup where possible (content and marketing pages), and [**render pages on-demand where it's not**](#static-vs-on-demand-rendering). This gives us the "best of both worlds", and allows us to incrementally optimize our site without a big architectural shift.

We should [**keep our existing Apollo GraphQL-based data layer**](#data-layer), because it allows us to keep data requirements neatly co-located with usage & we don't want to be stuck re-writing tons of components. Similarly, I like that Next.js allows us to [**co-locate route data-fetching with components rather than in a monolithic "build" script**](#routing-layer).

While all of these frameworks offer a [**great out-of-the-box developer experience**](#developer-experience), I found that Next.js aligned best with our existing technologies. It's also been [**battle-tested for large web applications**](#ecosystem--adoption) (like [Hulu](https://medium.com/hulu-tech-blog/introducing-hulus-modern-web-stack-d0be4cf4f439) and [Twitch](https://twitter.com/rauchg/status/1069683668132581376)), and not just simpler content/marketing sites.

While [JAMStack](https://jamstack.wtf)-centric hosting providers (like [ZEIT Now](https://zeit.co) and [Netlify](https://www.netlify.com)) offer incredibly smooth setup, they're also very opinionated. For now, I'd recommend we [**continue to host on Heroku and Fastly**](#hosting) for a smoother transition.

See [DoSomething/phoenix-next#1970](https://github.com/DoSomething/phoenix-next/pull/1970) for a working proof-of-concept.

<br/>
<p align="center">
:dizzy:
</p>
<br/>

## Alternatives & Comparison

The two most popular React.js frameworks are definitely [Gatsby](https://www.gatsbyjs.org) and [Next.js](https://nextjs.org).

I also looked into [RedwoodJS](https://redwoodjs.com), a new serverless web application framework (from Tom Preston-Warner, of [Jekyll](https://jekyllrb.com) fame). It's far too early to recommend adopting in production, but it has a lovely [philosophy](https://github.com/redwoodjs/redwood#the-redwood-philosophy), some very smart conventions ([cells](https://redwoodjs.com/tutorial/cells), omg!) and a [fantastic tutorial](https://redwoodjs.com/tutorial/welcome-to-redwood.html). Definitely worth keeping an eye on!

We could [follow the Warren campaign's approach](https://dev.to/itsjoekent/server-side-rendering-react-in-realtime-without-melting-your-servers-21ej) and build our own Lambda/Node.js renderer using React's [`renderToString` API](https://reactjs.org/docs/react-dom-server.html) or [Puppeteer](https://developers.google.com/web/tools/puppeteer/articles/ssr). That would allow us to build a tool that perfectly matches our needs, but we'd also be "blazing our own trail" and losing many of the benefits of the vibrant communities that have adopted these frameworks! Because we're a small team, I've ruled that out as well.

Regardless of which approach we chose, we'd keep most of the same application code – so we're really just comparing the "renderer", data-fetching layer, developer tooling, and community/documentation:

### Static vs. On-Demand Rendering

There are two different approaches we could take to server-rendering: either pre-rendering static HTML files (like the old days), or server-rendering pages on-demand & caching them with a CDN. In both cases, we'd be transforming React components into HTML – just at different points in the process.

#### Static Rendering

Turning Phoenix into a completely static HTML site is a very tempting idea since it drastically reduces the operational complexity of serving the site (it's just HTML!). Unfortunately, it accomplishes this by shifting the complexity into the build process. In addition to building all of our application code & styles, we'd _also_ need to build all of our content too.

Gatsby is only able to statically render HTML, and so _everything_ we want in the server's response needs to be known at build time – requiring us to somewhat tediously tally up all of our content every time we build the site. I've heard this can get very slow (in some cases, hour-long builds!), although [Gatsby Build seems to help](https://www.gatsbyjs.org/blog/2020-01-27-announcing-gatsby-builds-and-reports/#builds-making-deploys-as-fast-as-our-websites)

Ideally, we could build each page on-demand as it changes so that content changes would remain fast. We'd still need to run a full build for app changes, though, and incremental builds for Gatsby [are not yet shipped](https://www.gatsbyjs.org/blog/2020-01-27-announcing-gatsby-builds-and-reports/#are-builds-the-same-thing-as-incremental-builds).


#### On-Demand Rendering

Our website also often veers into "application" territory, since many of our pages have stateful interfaces (for example, the campaign page changes based on whether you're signed up or not). Even some otherwise simple "content" pages have dynamic elements, like how our [referral page](https://www.dosomething.org/us/join?user_id=5543dfd6469c64ec7d8b46b3&campaign_id=9037) is customized with the referrer's first name. We could hardly pre-render that page for every single user that might possibly refer someone!

For dynamic content like this, the easiest solution is to render pages or components on-demand: each time a user visits a page, the server renders the React application to HTML and sends that as the response. The user's browser can display that markup as soon as it's downloaded & then render any interaction or navigation from there on the client-side.

At first glance, we're just moving the same work from the client to the (admittedly, often faster) server. However, we've got a trick up our sleeve – caching! Once we've rendered a page once, we can store it in a public cache (on [Fastly](https://www.fastly.com) or [Zeit's CDN](https://zeit.co/smart-cdn)), making subsequent visits to that page "render" instantaneously.

#### Static Rendering & On-Demand Rendering

Next.js supports [opt-in static generation](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation) alongside on-demand rendered routes. This means that pages that are completely static (say, an article page) can be rendered to HTML at build-time while other pages continue to be rendered on-demand. Unfortunately, this [doesn't work with Apollo's server-side rendering](https://github.com/DoSomething/phoenix-next/pull/1970#issuecomment-623726651) & so we're stuck with a choice between static generation & pre-rendering colocated queries.

#### Further Rendering Optimizations

However, what about things like displaying a user's personalized submission gallery? We don't want a tiny bit of personalization to prevent an entire page from being cached. In those cases, we can [skip particular GraphQL queries](https://www.apollographql.com/docs/react/performance/server-side-rendering/#skipping-queries-for-ssr) when server-rendering. In this example, that allows us to render the rest of the campaign template & any user-specific elements would simply "pop" in on the client-side!

We could also use [edge-side includes](https://github.com/dunglas/react-esi) to cache different components independently! This is a time-honored technique for efficiently caching dynamic pages on CDNs, and is supported by Fastly & other major CDNs.

### Data Layer

Gatsby has an ambitious [GraphQL-based data layer](https://www.gatsbyjs.org/tutorial/part-five/#source-plugins), which creates an application-specific GraphQL schema that ties together sources like Markdown files, CMS plugins, and external GraphQL APIs. This is an interesting idea, but it works a little awkwardly for our needs.

The biggest downside is that stitching an existing GraphQL API (like [graphql.dosomething.org](https://graphql.dosomething.org)) into Gatsby's schema requires prefixing query and type names (e.g. `Post` would have to become `DSPost`). Gatsby is also only able to server-render queries in top-level pages, or in components that use its own custom [`useStatic`](https://www.gatsbyjs.org/docs/static-query/) hook. Both of these limitations would require significant re-writes of existing code.

In Next.js, we can fetch data using Apollo's [server-side rendering support](https://www.apollographql.com/docs/react/performance/server-side-rendering/). This allows us to server-render an entire page of GraphQL-backed components with no changes to our existing data fetching logic! And anything that doesn't make sense to query via GraphQL can be fetched with [`getServerSideProps`](https://nextjs.org/docs/basic-features/data-fetching).

### Routing Layer

Both Gatsby and Next.js provide their own built-in routing systems. Both frameworks use the filesystem to add simple routes, but they differ in how they handle dynamic routes (like our `us/facts/{fact}` pages):

Next.js uses declarative [dynamic routes](https://nextjs.org/docs/routing/dynamic-routes) – you say what your routes look like (e.g. `us/facts/[fact].js`) as the page component itself. It can then be rendered on-demand as a request comes in, or (optionally) pre-rendered by using the [`getStaticPaths`](https://nextjs.org/docs/basic-features/data-fetching#getstaticpaths-static-generation) hint. This allows us to keep all of a page's concerns in one place:

```js
// us/facts/[fact].js
const FACT_PAGE_QUERY = gql`
  page(slug: $slug) {
    title
    subTitle
    content
    ...
  }
`;

const FactPage = () => {
  const { query } = useRouter();
  const { data, loading, error } = useQuery(FACT_PAGE_QUERY, {
    variables: { slug: `facts/${query.fact}` },
  });

  return <div>...</div>;
};

export default withApollo(FactPage);
```

In contrast, Gatsby uses an imperative build script to create "dynamic" routes. You'd query all of the pages up front in `gatsby-node.js`, and then iterate over them to [create each page](https://www.gatsbyjs.org/docs/routing/#pages-created-with-createpage-action) based on a template & pass it data:

```js
// gatsby-node.js
exports.createPages = async ({ graphql, actions }) => {
  const { createPage } = actions;

  const { data } = await graphql(gql`
    query {
      dosomething {
        allPages {
          slug
          title
          subTitle
          content
          ...
        }
      }
    }
  `;

  data.dosomething.allPages.forEach(data => {
    createPage({
      path: `/us/facts/${data.slug}`,
      component: path.resolve(`./src/templates/fact.js`),
      context: { data },
    });
  });
};
```
```js
// src/templates/fact.js
const FactPage = ({ data }) => {
  // ...

  return <div>...</div>;
};
```

(Note that this example is still somewhat simplified, since we'd likely end up needing to iterate over multiple pages of GraphQL results for this build process in the real world. I'm also not sure how we'd efficiently cache this `allPages` query.)

### Developer Experience

Developer experience for both frameworks is great, and offer big improvements over what we're used to!

More than anything else, I'm looking forward to being able to follow the framework's established conventions (where previously we've had to forge our own path). Because these frameworks have a standardized approach to routing, data fetching, and tooling, we'll spend less time [bikeshedding](https://en.wikipedia.org/wiki/Law_of_triviality) and have a much easier time onboarding new engineers (since if you know the framework, you'll know how we use it).

These conventions also enable some exciting features. For example, both frameworks support "hot reloading" out of the box – which means that changes are _immediately_ reflected in the browser without reloading the page! It feels like magic. They also both display compilation errors in the browser (so no more tabbing over to the terminal to check if the build failed):

![gatsby-apollo-error](https://user-images.githubusercontent.com/583202/78587895-b1babc80-780b-11ea-9af7-88e2ccad22be.png)

Because they're both full-featured frameworks, we get a lot of things out of the box that we've previously had to set up for ourselves. No more worrying about maintaining and updating our own Webpack and Babel configurations! Both support styling via plain CSS, SCSS, [CSS Modules](https://github.com/css-modules/css-modules), or CSS-in-JS libraries like [Emotion](https://emotion.sh).

Interestingly, Next.js only added built-in support for global CSS stylesheets [early this year](https://nextjs.org/blog/next-9-2#built-in-css-support-for-global-stylesheets). To protect developers from CSS's [global namespace and other problems](https://speakerdeck.com/vjeux/react-css-in-js-react-france-meetup), they also limit applications to [_only_ importing CSS within the root application](https://err.sh/next.js/css-global) component. This will require us to rewrite some old SCSS styles, which is a decent chunk of work. I think that is time well-spent since it removes some unexpected "footguns" and encourages us to [continue our investment in Tailwind & Emotion](https://github.com/DoSomething/rfcs/blob/master/005-tailwindcss-framework.md)!

### Ecosystem & Adoption

One of our architectural tenets is to [stick with "boring" technology](http://boringtechnology.club/) whenever possible. As a small shop, we don't want to be the ones hitting new edge cases & stretching the limits of what a particular tool is capable of.

While server-side rendering is hardly _boring_, we're not blazing our own trail. The production websites for [Hulu](https://medium.com/hulu-tech-blog/introducing-hulus-modern-web-stack-d0be4cf4f439), [Twitch](https://twitter.com/rauchg/status/1069683668132581376), Nike, and AT&T all run on Next.js; and Gatsby is responsible for rendering some [high-profile marketing sites](https://www.gatsbyjs.org/showcase/) and the [ReactJS documentation](https://reactjs.org). Both frameworks have vibrant communities, and when I did run into an issue I was always able to find someone else who'd tackled it first.

### Hosting

There are a number of "serverless" options for hosting a Gatsby or Next.js site – [ZEIT Now](https://zeit.co), [Netlify](https://www.netlify.com), or AWS S3/Lambda. There are also an abundance of static-site hosts (like [Surge.sh](https://surge.sh)) and edge workers (like [Cloudflare Workers](https://workers.cloudflare.com), where computation is done on CDN edge nodes).

#### ZEIT Now
ZEIT Now supports Next.js and all of its features out of the box (as you might expect, considering it's made by the same team). It runs [atop established cloud providers](https://zeit.co/docs/v2/network/regions-and-providers), but offers a significantly nicer developer experience. And as promised, deploying code to Now was super easy!

The biggest drawback is that you're [forced to use their CDN](https://zeit.co/docs/v2/network/frequently-asked-questions#what-if-i-have-an-existing-cdn-like-akamai,-fastly,-cloudflare), which means we'd have a hard time splitting traffic with our existing backend via Fastly. There's also no support for managing configuration-as-code with Terraform.

#### Netlify
Netlify primarily focuses on static-site hosting. They also offer [Netlify Functions](https://www.netlify.com/products/functions/), a [user-friendly wrapper](https://docs.netlify.com/functions/overview/) around AWS Lambda. (These both support configuration [via Terraform](https://www.terraform.io/docs/providers/netlify/index.html), which is a huge plus in my book!)

Unfortunately, their focus on static-site hosting means Netlify doesn't support on-demand server-side rendering out of the box (although there are somewhat hacky [ways to get it to work](https://medium.com/@finn.woelm/how-to-deploy-nextjs-on-netlify-with-server-side-rendering-9e8c06a06e77)). And like ZEIT Now, Netlify [requires that you use their CDN](https://www.netlify.com/blog/2017/03/28/why-you-dont-need-cloudflare-with-netlify/).

In both of these cases, we'd be trading slightly higher costs (and reduced flexibility) for much simpler operations. This is a trade-off that we've happily made in the past (like our switch from EC2 to Heroku!), since we have a very small team & limited bandwidth for operations.

However, being limited to a third party CDN is almost certainly a dealbreaker – it makes it impossible for us to gradually swap traffic to a new backend & would require re-writing our existing redirect logic (powered by Fastly's edge dictionaries) for a whole new stack.

#### Heroku

Because of those limitations, I'd recommend an initial rollout as a [traditional Node.js application](https://nextjs.org/docs/deployment#nodejs-server) on Heroku. This allows us to keep the same simple deployment infrastructure we're used to, while [running a parallel application with the new server-side renderer](https://elements.heroku.com/buildpacks/heroku/heroku-buildpack-multi-procfile). And while we'd be losing out on a lot of the scalability wins that are unlocked by moving to a serverless platform, there's nothing stopping us from making that additional move in the future.

## Drawbacks

While adding server-side rendering reduces context switching between two codebases (PHP and React), it introduces its own overhead since we can now render one codebase in two environments (Node.js for SSR & browser). Developers will need to be aware of these two differing "modes" a component may be rendered in, and re-write code appropriately (such as being careful about `window` usage).

Server-side rendering is also still rather resource intensive, and we'll be heavily relying on caching to avoid overloading our backend. Luckily, we can always fall back to client-side rendering if necessary, and can use [`stale-while-revalidate`](https://www.fastly.com/blog/stale-while-revalidate-stale-if-error-available-today) to make slower requests to the backend without slowing down user-facing requests.

## Additional Resources
- Next.js: [Tutorial](https://nextjs.org/learn/basics/getting-started), [Documentation](https://nextjs.org/docs/getting-started), [Proof-of-Concept (DoSomething/phoenix-next#1970)](https://github.com/DoSomething/phoenix-next/pull/1970)
- Gatsby.js: [Tutorial](https://www.gatsbyjs.org/tutorial/), [Documentation](https://www.gatsbyjs.org/docs/), [Proof-of-Concept (DoSomething/phoenix-next#2025)](https://github.com/DoSomething/phoenix-next/pull/2025)
