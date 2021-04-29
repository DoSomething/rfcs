# Legacy Code Workflow

We should establish a transition workflow for how we refactor and swap out legacy code components or encompassed-logic.

**Discussion:** <https://github.com/DoSomething/rfcs/pull/16>

## Problem

When we are replacing an established component with a new component or replacing encompassed code logic in a function or class, the process may take multiple pull requests or steps to complete to avoid issues with the legacy code currently in use.

As a developer proceeds through the process, they need a better way to transition replacing the existing code without causing downtime or confusing other developers as to which code approach should be used and which one is being deprecated.

## Proposal

To avoid downtime and remove confusion to developers the proposal is to begin the process by renaming the existing component/class/method/function and prefixing it with `Legacy*`. This would be the first pull request when tackling the task.

For example, a component called `EmailSubscriptions` would be renamed to `LegacyEmailSubscriptions`, and all instances where it is used would be renamed as well in the first pull request for review.

This would immediately highlight to other developers that the encompassed code is being deprecated and should potentially utilize the new version (ideally named the same, but without the `Legacy*` prefix).

If the new encompassed code is renamed to something different, the legacy code with the `Legacy*` prefix could have a code comment indicating the new name of the code that is replacing it.

_Note:_ When available, it would also be highly recommended to include a `@deprecated` in the code block for the legacy code.

An added benefit of renaming the legacy code with the `Legacy*` prefix is that it would encourage the developer to also scour the codebase to find all iterations of the old, legacy code, rename it and thus highlight all instances where it is used so they familiarize themselves with where all the changes will be taking place. This would provide a better overall view of everything that will be affected by the changes.

With the aforementioned example, `LegacyEmailSubscriptions` would highlight to other developers that this component is being deprecated, and instead they should refer to the new `EmailSubscriptions` component, or comment in the legacy component for additional reference.

## Drawbacks

The main drawback would be that this process includes some additional work upfront to rename legacy code with the prefix and all iterations where it is used for the initial pull request.

However, as mentioned, this initial work would help familiarize the developer with the changes and the extent of what needs to be replaced/edited.

## Alternatives

Within a team discssion on Slack, the wise puppet-master himself and DoSomething alumnus (RIP), Aaron, suggested maybe placing the renamed legacy code within a `legacy` directory that could afterwards be deleted when the code is finally removed.

The benefit of this would be that it would be one ideal spot to contain all legacy code.

This could be a useful idea, but not all code would be easily moved from one directory to another directory. It would be easier for component code, but for classes, methods or functions it would be more ideal and possibly required for them to stay in their original locations until the new code is available to replace them. It would also add an additional step of needing to move legacy code all to one spot along with renaming them.
