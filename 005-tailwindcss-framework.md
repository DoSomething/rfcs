# Tailwind CSS Framework

We should implement the use of [Tailwind CSS Framework](https://tailwindcss.com/) on our user and admin interfaces to help simplify front-end development across our team.

**Discussion:** <https://github.com/DoSomething/rfcs/pull/5>

## Problem

It has become more difficult over time for our tech team to confidently update the styling and design patterns of our front-end user interfaces.

While our pattern library, [Forge](https://github.com/DoSomething/forge), worked great for building interfaces with the established patterns it provided, it has not been updated in some time and with our rapidly changing design needs and team structure, it no longer seems like the ideal solution to continue evolving.

As our team of full-stack developers shifts to doing more work on our projects that includes both back-end and front-end changes, we need a styling framework in place that is flexible, customizable and empowers our developers to confidently use without the need to code custom style declarations themselves.

Additionally, the patterns in Forge became very specific to how the DoSomething.org website looked in prior iterations. As the design evolved, it has forced us to override many of Forge's rules to be able to replicate changing design needs.

## Proposal

By utilizing the Tailwind CSS Framework the tech team can work more confidently when building the front-end different interfaces.

A utility class framework like Tailwind CSS offers the following benefits:

- We can easily style items and pages by applying a pre-configured library of classes directly to the HTML.
- We can style custom components without needing to write custom CSS, and without needing to invent specific and unique class names for each component.
- Once we establish our collection of utility classes, a project's CSS stops growing, since there is less need for custom CSS.
- Helps keeps CSS scoped to a component, resulting in less worries that a style change could affect styles globally.
- We will be building interfaces with proper constraints defined by the framework's configuration; no more magic pixel value numbers.
- The utilities would help developers adhere to the predefined design system, keeping our interfaces consistent.
- Support for responsive design and pseudo-classes within the series of generated utility classes.
- If needed, we can extract components when it makes sense.

`@TODO: Add bit about Forge 2.0.`
