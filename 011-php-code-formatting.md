# PHP Code Formatting

We should implement the use of both the [Prettier PHP Plugin](https://github.com/prettier/plugin-php) for prettifying the look of our PHP code and the [PHP Coding Standards Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer) tool for ensuring the resulting PHP code is written well, following specified code quality rules.

**Discussion:** <https://github.com/DoSomething/rfcs/pull/11>

## Problem

For most of our repositories that include PHP we currently depend on [StyleCI](https://styleci.io) for formatting our code.

While it has served our needs for the most part, this tool is not as convenient as some other tools available, and at times has even been a bit confusing to use.

The setup for StyleCI includes adding a GitHub repository to the list of projects it should "check" automatically for each "push" to a pull request branch. The settings can be customized on the StyleCI web interface but each project can also contain their own configuration in a `.styleci.yml` file. Having multiple locations for configuration settings can be a bit confusing; not always being apparent if a project is checked by StyleCI if the local config file is not present.

When StyleCI runs to check the PHP code style on a push to a branch, if it finds errors, the run fails with a link to details in the pull request. At times these details are very confusing to understand exactly what the recommended change is. Developers sometimes need to try a few edits, commit them and see if the styles pass on subsequent runs, but there is no way to know without pushing up code.

StyleCI does offer to create pull requests on demand for these changes, but that workflow feels a bit disjointed given that a developer would like this all fixed in their initial pull request.

It would be ideal to be able to know exactly what needs to be changed and confident that doing so has fixed the style issue; and not needing to push changes numerous times to get feedback from StyleCI on whether the changes pass or not.

## Proposal

We should implement the use of both the [Prettier PHP Plugin](https://github.com/prettier/plugin-php) for prettifying the look of our PHP code and the [PHP Coding Standards Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer) tool for ensuring the resulting PHP code is written well, following specified code quality rules.

While there is going to be some natural overlap between the two tools and how they format code style, we can definitely benefit from where the two tools differ in what they are meant to fix, to get the best of both worlds.

As the [Prettier docs explain](https://prettier.io/docs/en/comparison.html), Prettier's strength lies in removing the stress of how the code "looks", and ensures it is formatted the same across a team of developers. However, it does nothing to help with code quality rules and this is where PHP CS Fixer can fill that gap, and replace the role of what StyleCI did in a much more convenient and useful way.

### Prettier PHP Plugin

[Prettier PHP Plugin](https://github.com/prettier/plugin-php) is an opinionated code formatter that will rewrite outputted PHP code that conforms to a consistent style, aiming for a line length print width of 80 characters.

It helps developers eliminate any concerns regarding how their PHP code looks because Prettier takes care of that for them.

You can see a [pull request](https://github.com/DoSomething/rogue/pull/1094) on our Rogue application with how the Prettier PHP Plugin is configured and how it proceeded to format the code.

This [commit](https://github.com/DoSomething/rogue/pull/1094/commits/5e2a94b46168539a6346b6057efc2d915e444626) specifically shows how the Prettier PHP plugin is set up to run along side the configuration for our JavaScript code.

This [commit](https://github.com/DoSomething/rogue/pull/1094/commits/88a8ed7d2e5f8d3e13e93c4840b2d389cd331614) specifically shows all the files changed when the `npm run format` command was executed.

The way the plugin is configured, in daily use developers will not need to manually run the command to format their code since it will run as a `pre-commit` hook on any changed files.

Additionally, some code editors (like Visual Studio Code) easily support running Prettier on file saves (refer to the code editor docs for documentation on setup).

### PHP Code Standards Fixer

The [PHP Coding Standards Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer) tool cares less about the "look" of the PHP Code, and more about the code quality. It focuses on automatically fixing PHP code to follow a specific set of code quality rules, and in some scenarios it can even modernize and optimize the code.

It helps developers eliminate any concerns regarding the quality of their PHP code, whether they are adhering to PSR standards and helps with linting their code.

You can see a [pull request](https://github.com/DoSomething/rogue/pull/1092) on our Rogue application with how the PHP CS Fixer tool is configured and how it proceeded to format the code.

This [commit](https://github.com/DoSomething/rogue/commit/eeca6efb6b29df01e13e816b7d051e872b8cd2f7) specifically shows how the PHP CS Fixer tool is set up. The process is simplified by utilizing the [weerd/php-style](https://github.com/weerd/php-style) package which contains easily accessible rulesets for both generic PHP packages and Laravel projects.

This [commit](https://github.com/DoSomething/rogue/pull/1092/commits/d48ec9a1a759941c49c5e687a80b639e9d808fed) specifically shows all the files changed when the `composer format` command was executed within the Homestead vagrant environment.

Since this tool is run as a composer command, based on most of our development setups, the command should be run within the Homestead vagrant box. In practice, developers can potentially run the command manually, but it is also setup to run within out Continuous Integration environment and it will flag any failures within a pull request (similar to how StyleCI behaves).

However, once flagged in a pull request, to automatically fix these error, developers can just run the `composer format` command in Homestead and then commit and push up their code, confident that the errors were addressed and the style checks should pass.

## Drawbacks

// WIP...

## Additional Resources
