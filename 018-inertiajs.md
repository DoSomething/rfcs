# Inertia JS

We should implement the use of [Inertia JS](https://inertiajs.com/) to simplify the development of the frontend user interfaces for our administrative backend.

## Problem

We are in the process of consolidating and merging multiple backend services into a single codebase. Each of these services had their own frontend administrative interfaces, and we will need to unify them as well.

As a team, we have found that moving to a component-based architecture for the frontend using [React](https://reactjs.org/docs/getting-started.html) and [Tailwind](https://tailwindcss.com/docs) has helped us re-use code more efficiently and made it easier to maintain existing features and develop new ones.

However, to implement a component-based architecture for the administrative frontend of our consolidated backend would require a lot of work to expose numerous new API endpoints and connections that a component-based architecture using React would hook into, just to enable the administrative interface.

It would be ideal if we could maintain the backend code and have it communicate easily with the frontend without having to add a lot of confusing duplicate code or connectors and endpoints that would then need to be maintained.

This is where Inertia JS would excel at providing the ideal solution; it would allow us to enable a fully client-side rendered frontend and provides the glue to connect it to our existing server-side Laravel PHP code.

The updates to connect the pieces are minimal and gets the team up and running to develop our administrative frontend utilizing React components and Tailwind.

## Proposal

Implement the use of [Inertia JS](https://inertiajs.com/) in Northstar for our administrative interface.

The overall aim is to reduce maintenance for our team, particularly when adding or updating new features to our administrative interfaces.

Inertia will allow us to use the best of both worlds; a client-side rendered interface with reusable React components and server-side business logic utilizing the robust and familiar Laravel framework without worrying about additional code to pass data to the React components.

There will be an initial ramp up to build out the core components for our administrative interfaces. However, we can build on the experience we have obtained from component-izing our user facing frontend. We also already have some components from our activity service that have been built using React, and we could easily update those to receive data props from Inertia. Once we have an established baseline of admin components most of the admin pages will be easier to build out and/or update.

The following is a proposed approach for implementing Inertia JS in Northstar:

- Add Inertia JS to Northstar and set it up so it works with our existing code.
- Implement a new main layout for the admin interface.
- Consolidate all routes into Inertia routes that will pass data as props.
- Build out the interface using React components that utilize the data passed via the Inertia routes.

## Drawbacks

For the most part there do not appear to be any significant drawbacks with implementing Inertia JS. It seems like the right "happy middleground" that would let us maintain most of our existing Laravel backend work, yet provide the flexibility to use component-based interface elements built in React.

One potential drawback is that the team would be required to update and create admin interface elements; especially given how specialized some of our needs can be at times. However, I imagine that after building a number of initial admin interface elements in React as components, the level of effort would drop as components can be re-used.

Another potential drawback is that Inertia just has not been around for that long. The [Inertia adaptor for Laravel](https://github.com/inertiajs/inertia-laravel) is currently tagged at version `v0.4.1` and the [Inertia adaptor for React](https://github.com/inertiajs/inertia/blob/master/packages/inertia-react/package.json) is currently at version `v0.5.12`.

That being said, it has had a lot of support within the Laravel community and a lot of recent commit activity. The documentation is very thorough and it even has an entire [live example demo site](https://inertiajs.com/demo-application) built using Laravel, InertiaJS and VueJS with the code available as an [open source repository](https://github.com/inertiajs/pingcrm). The community has even contributed versions of the demo site using other server-side and client-side languages/frameworks, including one using [Laravel, InertiaJS and ReactJS](https://github.com/Landish/pingcrm-react).

There is even a [showcase website](https://builtwithinertia.com/) for sites officially built using Inertia JS, and that submit themselves to be in the showcase. I did also find a post on Reddit that poses a question regarding who is using Inertia JS, and the [author of the package responds](https://www.reddit.com/r/laravel/comments/f2ydv0/is_there_anyone_here_use_inertiajs_and_what_is/fhg3557/?utm_source=reddit&utm_medium=web2x&context=3) to provide some additional input.

In his response he also mentions the following:

> Finally, at the end of the day Inertia is actually a pretty thin layer between your app's back-end and front-end. If you ever decided that Inertia wasn't working for you, removing it would be a relatively trivial task.

I thought this was insightful and knowing that if one day we changed our minds, removing Inertia JS from our code would not necessarily be a heavy lift (most of the work would be in finding/building a different solution to connect our data to the React frontend).

## Alternatives

There are a couple of alternatives we also considered, but with either one it would potentially mean either less flexibility, or lots of overhead and more maintenance to enable our existing backend code to communicate with the frontend.

### Laravel Nova

[Laravel Nova](https://nova.laravel.com/) is a one-time purchase that essentially provides a full administrative panel for a Laravel application.

It has a whole slew of features that are very compelling. The main benefit Nova provides is that the team would not have to do much work building out an administrative panel.

However, I am cautious that how our Eloquent models have evolved and the different relationships we have established between resources, combined with the merging of services may not completely follow the Laravel approach and we could run into numerous headaches.

I also imagine we would have to wait until we complete the unified database and switch off MongoDB completely before we could move to use Nova, to avoid issues since the MongoDB Eloquent driver is a community contribution and not officially supported by Laravel.

Nova does offer CLI generators to create custom tools within the framework that could support our business needs. The downside is that these tools are generated as Vue components, which would require our developers to learn [Vue JS](https://vuejs.org/) and context switch between Vue JS for administrative tools and React JS for member facing tools. While the need for custom tools may be low, it would imply additional maintenance overhead in a client-side framework the team is not familiar with.

### React Admin

[React Admin](https://marmelab.com/react-admin) is an open-source framework for building admin applications in React with data provided by REST/GraphQL APIs.

The framework looks like it has a number of useful components to help build out an administrative interface. The main benefit React Admin provides is that the team would not have to do much work building out components for an administrative interface.

However, the framework is dependent on having established API endpoints for all the resources it will render. This would require the team to invest time and effort into building out endpoints for all backend resources that would be administered via a frontend UI and maintaining all the endpoints over time.

Requiring API endpoints for all resources available to both the admin frontend and external dependent services would mean that there will be many endpoints that overlap and service both, along with a number of endpoints that are only used by one or the other. This could become a maintenance headache in knowing which endpoints are used where.

Endpoints that do overlap usage on both the admin frontend and external service could also become a maintenance headache in needing to setup and utilize feature-flags or version control the endpoints when changes are made. The team would likely need to be less nimble and slower with changes to API endpoints, careful that a change made for the admin frontend API response does not break functionality with how external services utilize the same response.

This is not to say that versioning API responses is a bad approach, but when the API endpoint is being used for both an internal admin frontend interface and an external service frontend it could complicate things pretty quickly, balancing between versioned endpoints. Given the size of our current team, less complexity would be ideal!

It would be ideal that any API endpoints the team has to maintain for our backend service are only those that other services rely on, and not for the sole purpose of presenting a client-side admin interface.

One of the benefits of React Admin is that it comes with a slew of pre-built components. There is no reason we could not use a simpler package that provides just pre-built components without the included architecturea and routing control that React Admin requires. This idea opens the possibility of utilizing a "combo" approach, described below.


### Combo

One possibility that was also considered is the implementation of Inertia JS as the adaptor for our backend and connecting it to React Admin for the pre-built React admin components. 

While this sounds useful, the React Admin framework, as mentioned, relies heavily on API endpoints for resources and would not combine well with the approach Inertia JS utilizes to pass data with its built-in connectors.

That being said, there could be other React admin libraries that could supply more basic components and alleviate the need to build our own.

I think it would benefit the team to potentially build custom components that could be shared with both the Inertia adapted Laravel backend and the member facing frontend UI, but all the endpoints is something the team could discuss further and not dependent on the core of this RFC to decide on implementing the use of Inertia.