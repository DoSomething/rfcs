# Tailwind CSS Framework

We should implement the use of [Tailwind CSS Framework](https://tailwindcss.com/) on our user and admin interfaces to help simplify front-end development across our team.

**Discussion:** <https://github.com/DoSomething/rfcs/pull/5>

## Problem

It has become more difficult over time for our tech team to confidently update the styling and design patterns of our front-end user interfaces.

While our pattern library, [Forge](https://github.com/DoSomething/forge), worked great for building interfaces with the established patterns it provided, the workflow required (NPM link to test changes locally, publishing releases, updating apps to use new release) did not make updates and rapid iteration to designs patterns feasible, causing Forge to be slow with regular updates and improvement.

We do appreciate having a library approach to our front-end to help with establishing a system and a shared baseline of patterns, but in practice a CSS library that has simpler updates or just requires less updates while still supporting changing design needs would be an improvement.

As our team structure and focus has changed from dedicated back-end and front-end developers to full-stack developers, we have less team members with extensive, in-depth CSS knowledge, experience or interest in writing cross-browser, responsive style rules. However, our full-stack team still needs to tackle development of features that include full styling of interfaces and showcases the need for a styling framework that is flexible, customizable and empowers our developers to confidently apply styles without the need to code custom style declarations themselves.

## Proposal

A Utility CSS Framework is a system that provides low-level utility classes that typically map to a corresponding single style rule. They enable you to build custom interfaces entirely with these classes in the HTML for an interface without the need to context-switch languages back-and-forth between HTML and CSS.

Additionally, by using utility classes to style HTML, it has the added benefit of abstracting away specificity issues and eliminating problems with the global namespace that can cause style conflicts.

By utilizing a Utility CSS Framework, and more specifically the TailwindCSS Framework, the tech team can work more confidently when building the front-end for different interfaces across our platforms.

A utility class framework like Tailwind CSS offers the following benefits:

- Establish an API of class names for DoSomething's design style guide.
- Easily style components and pages by applying a pre-configured library of classes derived from our established style guide directly to HTML.
- Simplify styling components by significantly reducing the need to write custom CSS, and no more worrying about coming up with unique [BEM-style class names](http://getbem.com/introduction/) as designs change or new designs are developed.
- Limits the growth of CSS styles once our collection of utility classes is established and using a library like [PurgeCSS](https://www.purgecss.com/) reduces CSS size further by removing unused utility class definitions in an application.
- Utility CSS frameworks can not realistic cover every possible style need with their establish library of classes, and Tailwind has useful [plugin system](https://tailwindcss.com/docs/plugins/) to add additional classes needed by a project with full support for outputting classes for responsive support, pseudo-states, etc.
- Helps keeps CSS scoped to a component, resulting in less worries that a style change could affect styles globally or items along the CSS cascade.
- Build more consistent interfaces thanks to utility classes generated based on a configuration file that has been set up using values established in conjunction with the Product Team.
- Ideally mitigate the use of "magic pixel value numbers" when styling an interface. While such styling may appear, with an established utility class framework in place it _will_ make it very obvious when a developer resorts to using specific pixel value style definitions _instead_ of using an established utility class and can be discussed in a pull request. It will also help in highlighting if we need to potentially add new utility classes to un-magicify aforementioned magic pixel style rules that may appear.
- Support for [responsiveness](https://tailwindcss.com/docs/responsive-design) and [pseudo-classes](https://tailwindcss.com/docs/pseudo-class-variants) within the library of generated utility classes.
- Tailwind also provides the ability to extract small components of repeating utility class combinations when needed. While Tailwind encourages always using utility classes in your HTML, there can be scenarios where you may want to extract a component that includes multiple utility classes into a class for use in various places. Since we use React and build most of our interfaces as components, this will be unlikely, but it's a nice feature that Tailwind provides to help with maintainability if some block of HTML is not componentized. See the [Tailwind documentation](https://tailwindcss.com/docs/extracting-components) on extracting components for more information.

## Forge 7.0

We think that for the next version of Forge, it could embrace the utility class approach and still be the go-to package for styling front-end interfaces on DoSomething applications.

For version 7.0 we propose eliminating all the current patterns in Forge (as our projects switch to using Tailwind, they will stop using these patterns as is and thus no longer needed). Instead Forge would house the main TailwindCSS configuration and provide the generated styles from this custom configuration. Projects that use Forge 7.0 package can include these generated TailwindCSS styles and have access to all the utility classes they otherwise need.

Additionally, we also propose that Forge could house generic and very common React components that we conclude are useful to use across our DoSomething properties so that there is less duplicated components on different apps.
