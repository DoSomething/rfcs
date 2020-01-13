# Tailwind CSS Framework

We should implement the use of [Tailwind CSS Framework](https://tailwindcss.com/) on our user and admin interfaces to help simplify front-end development across our team.

**Discussion:** <https://github.com/DoSomething/rfcs/pull/5>

## Problem

It has become more difficult over time for our tech team to confidently update the styling and design patterns of our front-end user interfaces.

Our pattern library, [Forge](https://github.com/DoSomething/forge), has worked great for building interfaces with the established patterns it provided. However, the workflow required (using NPM link to test changes locally, publishing releases, updating apps to use new release) did not make updates and rapid iteration to designs patterns feasible, causing Forge to be slow with regular updates and improvements. As a result, developers on the team would often end up adding new patterns directly to an application and never migrating them to Forge for use in other applications.

We do appreciate having a library approach to our front-end to help with establishing a system and a shared baseline of patterns, but in practice a CSS library that has simpler updates or just requires less updates while still supporting changing design needs would be an improvement.

Additionally, as our team structure and focus has changed from dedicated back-end and front-end developers to full-stack developers, we have less team members with extensive, in-depth CSS knowledge, experience or interest in writing cross-browser, responsive style rules. However, our full-stack team still needs to tackle development of features that include full styling of interfaces and showcases the need for a styling framework that is flexible, customizable and empowers our developers to confidently apply styles without the need to code custom style declarations themselves.

## Proposal

A Utility CSS Framework is a system that provides low-level utility classes that typically map to a corresponding single style rule. They enable you to build custom interfaces entirely with these quick and easy to write classes in the HTML for an interface, without the need to context-switch languages back-and-forth between HTML and CSS.

Additionally, by using utility classes to style HTML, it has the added benefit of abstracting away specificity issues and eliminating problems with the global namespace that can cause style conflicts.

By utilizing a Utility CSS Framework, and more specifically the TailwindCSS Framework, the tech team can work more confidently when building the front-end for different interfaces across our platforms.

A utility class framework like Tailwind CSS offers the following benefits:

- Establishes an API of class names for DoSomething's design style guide.
- Easily style components and pages by applying a pre-configured library of classes derived from our established style guide directly to HTML.
- Simplify styling components by significantly reducing the need to write custom CSS, and no more worrying about coming up with unique [BEM-style class names](http://getbem.com/introduction/) as designs change or new designs are developed.
- Utility classes use functional naming (describe what they do) rather than semantic naming for composed classes, making it easier for full-stack developers to understand what styles are being applied directly to the HTML rather than having to reference a stylesheet and see what series of styles a semantic class is applying.
- Limits the growth of CSS styles once our collection of utility classes is established and using a library like [PurgeCSS](https://www.purgecss.com/) reduces CSS size further by removing unused utility class definitions in an application.
- Utility CSS frameworks can not realisticly cover every possible style need with their establish library of classes, and Tailwind has a useful [plugin system](https://tailwindcss.com/docs/plugins/) to add additional classes needed by a project with full support for outputting classes for responsive styles, pseudo-states, etc.
- Helps keep CSS scoped to a component, resulting in less worries that a style change could affect styles globally or items along the CSS cascade.
- Build more consistent interfaces thanks to utility classes generated based on a configuration file that has been set up using values established in conjunction with the Product Team.
- Ideally mitigate the use of "magic pixel value numbers" when styling an interface. While such styling may appear, with an established utility class framework in place it _will_ make it very obvious when a developer resorts to using specific pixel value style definitions _instead_ of using established utility classes and can be discussed in a pull request. It will also help in highlighting if we need to potentially add new utility classes to un-magicify aforementioned magic pixel style rules that may appear.
- Support for [responsiveness](https://tailwindcss.com/docs/responsive-design) and [pseudo-classes](https://tailwindcss.com/docs/pseudo-class-variants) within the library of generated utility classes.
- Tailwind also provides the ability to extract small components of repeating utility class combinations when needed. While Tailwind encourages always using utility classes in your HTML, there can be scenarios where you may want to extract a component that includes multiple utility classes into a class for use in various places. Since we use React and build most of our interfaces as components, this will be unlikely, but it's a nice feature that Tailwind provides to help with maintainability if some blocks of HTML are not componentized. See the [Tailwind documentation](https://tailwindcss.com/docs/extracting-components) on extracting components for more information.
- Repeating class names in HTML is actually pretty great for GZIP, resulting in smaller HTML files with an already reduced-size CSS file of utility classes.

Additional benefits:

- [TailwindCSS](https://github.com/tailwindcss/tailwindcss) is open source so we could contribute back and help further promote open source software!
- Actively maintained.
- Available Tailwind CSS IntelliSense plugin for [VS Code](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss).

## Drawbacks

Once Tailwind CSS is implemented, it will take our full-stack developers a little bit of time to familiarize themselves with the utility class syntax. However the Tailwind CSS documentation is very thorough and easy to search, acting as a great reference. The VS Code extension is also great for suggesting class names based on a project's configuration.

Depending on the complexity of a component, seeing a series of styles displayed as class names within the HTML could become a little harder to read and potentially difficult to spot typos in a class names or potential duplicates. This could be mitigated by establishing a rule whereby class names are added alphabetically or there may be an ESLint rule we could apply; something we can figure out best practices as a team.

There may also be some time to adjust and learn how to best apply utility classes. For example, whether it is best to apply a font-size utility class to a parent container like a `<div>` or to apply it to each individual child `<p>` tag with in parent container. These situations will be tackled and addressed as we encounter them, establishing best practices as a team.

## Alternatives

Tailwind CSS is the recommended utility framework in this proposal, but it is by no means the only utility css framework available. We reviewed and compared a few other alternatives when deciding on which framework to move forward with.

The following list specifies where these alternatives fall short in comparison to Tailwind CSS:

**[Basscss](https://basscss.com/)**

- Less customizable than Tailwind.
- Not as actively maintained.
- Extension via NPM package add-ons.
- Minimal resources and documentation.

**[Beard](http://buildwithbeard.com/)**

- Less customizable than Tailwind.
- Utilizes SCSS file-based customizations and mixin functions; we have discussed potentially moving away from using SASS in the future.
- Repository no longer maintained and has been archived.

**[Shed](https://tedconf.github.io/shed-css/index.html)**

- Less customizable than Tailwind.
- Customized via CSS variables.
- Not as actively maintained.
- Shortened class naming convention makes it difficult to immediately understand what the utility class is doing.

**[Tachyons](http://tachyons.io/)**

- Customizing requires installing numerous NPM packages and using command to copy styles into specific project and editing those files.
- Less customizable than Tailwind.
- Not as actively maintained.
- Documentation could be more helpful; (example, the docs are not clear on responsive styles and how to customize).

**[Turretcss](https://turretcss.com/)**

- Contains utility classes but also a framework with composed classes.
- Customized via CSS variables.
- Not as actively maintained.

Part of the added appeal of Tailwind CSS is that it uses the power of JavaScript to compose the library of utility classes and has been developed in a way that makes it very extensible, allowing you to write your own custom utilities as plugins, that output classes with all the responsive and pseudo class varieties required.

### Why not CSS-in-JS or CSS Modules?

Using a utility framework like Tailwind CSS does not completely discount the idea of potentially also using an approach with CSS-in-JS like [Emotion](https://emotion.sh/docs/introduction). The basic premise is that you end up writing CSS Styles within the JS component and they are compiled into a custom class specific for that component, helping avoid styling issues with global scope.

CSS Modules is similar in that it helps eliminate issues with the global scope but allows you to write composed classes that you can then import into your JS component and are then compiled into a custom class specific for that component.

However, utility classes inherently avoid styling issues with the global scope and a big part of the aim of using a utility framework is to simplify and minimize the need to write custom CSS in our components; writing custom CSS can potentially result in design variations in the outputted components. Having our full-stack developers select from a predefined list of utility classes helps maintain and enforce consistency across our projects, by limiting styles to those classes available that were generated by rules from a design guide. Rather than having access to millions of colors and a slew of different font-sizes, etc, developers will only have access to the colors and sizes defined by the theme.

We could potentially still see the opportunity to use CSS-in-JS in scenarios where a complex design could require additional styling that utility classes could not address. Some designs where there are very specific pixel size adjustments, etc and it could actually be helpful to maintain those specific cases as custom CSS within the JS for the component. This could establish a process where any required custom CSS is only added specifically to the component where it needs to apply.

We did end up doing a trial run using [Emotion](https://emotion.sh/docs/introduction) for a scenario where we needed very specific CSS styles to animate and rotate an SVG spinner. Emotion ended up working perfectly for this scenario, completely scoping the custom CSS styles to the component, working great in conjunction with some of the Tailwind utility classes used to further style the spinner component. We have decided to continue to move forward with Emotion as a suitable compliment for when a component needs very custom CSS styles.

## Forge 7.0

We think that for the next version of Forge, it could embrace the utility class approach and still be the go-to package for styling front-end interfaces on DoSomething applications.

For version 7.0 we propose eliminating all the current patterns in Forge (as our projects switch to using Tailwind, patterns will switch to being specifically components built out of utility classes). Instead Forge would house the main TailwindCSS configuration and provide the generated styles from this custom configuration. Projects that use Forge 7.0 package can include these generated TailwindCSS styles and have access to all the utility classes they otherwise need.

Additionally, we also propose that Forge could house generic and very common React components that we conclude are useful to use across our DoSomething properties so that there is less duplicated components on different apps.

## Additional Resources

### Articles

- [CSS Utility Classes and "Separation of Concerns"](https://adamwathan.me/css-utility-classes-and-separation-of-concerns/)

### Utility CSS Frameworks

- [Basscss](https://basscss.com/)
- [Beard](http://buildwithbeard.com/)
- [Shed](https://tedconf.github.io/shed-css/index.html)
- [TailwindCSS](https://tailwindcss.com/docs/utility-first/)
- [Tachyons](http://tachyons.io/)
- [Turretcss](https://turretcss.com/)
