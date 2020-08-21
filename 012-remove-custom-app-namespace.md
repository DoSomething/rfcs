# Remove Custom App Namespace

We should remove the custom application namespaces from the Laravel PHP projects that utilize them.

**Discussion:** <https://github.com/DoSomething/rfcs/pull/12>

## Problem

A few of our apps utilize custom application namespaces for classes, which adds to the level of complexity and thoroughness required when performing upgrades or running commands, etc.

Prior to Laravel 6.x, there was an [`app:name`](https://github.com/laravel/framework/blob/5.8/src/Illuminate/Foundation/Console/AppNameCommand.php) Artisan command you could run after initially setting up a project to rename the application namespace from `App\\` to something custom (e.g. we use `Auora\\` and `Rogue\\` in those respective projects).

As of Laravel 6.x, this Artisan command was removed ([it happened in the unreleased 5.9 version](https://github.com/laravel/framework/pull/27575) that become 6.x), since it did not serve much of a purpose.

Ultimately, it makes it easier when switching between apps, working on code, running console commands or when working in Tinker, etc to not run into errors because you happen to have forgotten to use the correct app namespace instead of just the simple default of `App\\`.

The issues with the custom namespace became very apparent during the Laravel upgrade process, where sometimes new files were added to the framework. After copying them into an application, if you had not remembered to change the app namespace in the copied file, you would run into errors because the namespace needed to be changed from `App\\` to whatever the custom one happened to be.

This would remove the need to remember that application namespaces differ between apps and in general it never really served us much of a purpose.

## Proposal

Change all projects with custom application namespaces back to using the default of `App\\`.

Since the command has been removed in Laravel 6.x, we cannot use it to change everything back and I suspect it was probably best run on an initial project install to begin with.

I think a simple find/replace to change instances of `CustomName\` to `App\` would do most of the work. We would need to test the app and make sure there are no straggling files. However, finding the custom name in most `*.php` files should be pretty easy in a competent code editor.

This would also require updating the `"psr-4"` property in the `"autoload"` setting `composer.json` with the `App\\` namespace:

```json
"autoload": {
  "psr-4": {
    "App\\": "app/"
  },
}
```

## Drawbacks

Only drawback that I can see is needing to ensure that we update all instance of the custom app namespace to the general `App\\`, including any scripts or continuous integration setups that could have it still set as the old value.
