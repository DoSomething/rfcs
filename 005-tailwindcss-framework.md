# Tailwind CSS Framework

We should implement the use of [Tailwind CSS Framework](https://tailwindcss.com/) on our user and admin interfaces to help simplify front-end development across our team.

**Discussion:** <https://github.com/DoSomething/rfcs/pull/5>

## Problem

It has become more difficult over time for our tech team to confidently update the styling and design patterns of our front-end user interfaces.

While our pattern library, [Forge](https://github.com/DoSomething/forge), worked great for building interfaces with the established patterns it provided, the workflow required (NPM link to test changes locally, publishing releases, updating apps to use new release) did not make updates and rapid iteration to designs patterns feasible, causing Forge to be slow with regular updates and improvement.

We do appreciate having a library approach to our front-end to help with establishing a system and a shared baseline of patterns, but in practice a CSS library that has simpler updates or just requires less updates while still supporting changing design needs would be an improvement.

As our team structure and focus has changed from dedicated back-end and front-end developers to full-stack developers, we have less team members with extensive, in-depth CSS knowledge, experience or interest in writing cross-browser, responsive style rules. However, our full-stack team still needs to tackle development of features that include full styling of interfaces and showcases the need for a styling framework that is flexible, customizable and empowers our developers to confidently apply styles without the need to code custom style declarations themselves.

## Proposal

By utilizing the Tailwind CSS Framework the tech team can work more confidently when building the front-end for different interfaces.

A utility class framework like Tailwind CSS offers the following benefits:

- We can easily style items and pages by applying a pre-configured library of classes directly to HTML.
- We can style custom components without needing to write custom CSS, and without needing to invent specific and unique class names for each component.
- Once we establish our collection of utility classes, a project's CSS stops growing, since there is less need for custom CSS.
- Helps keeps CSS scoped to a component, resulting in less worries that a style change could affect styles globally.
- We will be building interfaces with proper constraints defined by the framework's configuration; no more magic pixel value numbers.
- The utilities would help developers adhere to the predefined design system, keeping our interfaces consistent.
- Support for responsive design and pseudo-classes within the series of generated utility classes.
- If needed, we can extract components when it makes sense.

## Forge 7.0

We think that for the next version of Forge, it could embrace the utility class approach and still be the go-to package for styling front-end interfaces on DoSomething applications.

For version 7.0 we propose eliminating all the current patterns in Forge (as our projects switch to using Tailwind, they will stop using these patterns as is and thus no longer needed). Instead Forge would house the main TailwindCSS configuration and provide the generated styles from this custom configuration. Projects that use Forge 7.0 package can include these generated TailwindCSS styles and have access to all the utility classes they otherwise need.

Additionally, we also propose that Forge could house generic and very common React components that we conclude are useful to use across our DoSomething properties so that there is less duplicated components on different apps.
