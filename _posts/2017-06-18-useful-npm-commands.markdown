---
layout: post
title:  "Useful npm commands"
date:   2017-06-18
category: vim
backgrounds:
    - http://blog.jdonado.com/assets/images/backgrounds/npm-bg.png
thumb: http://blog.jdonado.com/assets/images/npm-icon.svg
tags: npm nodejs 
---

# Quick reference of some useful commands

## Avoid conflicts

If you've been using nodeJS npm package manager, chances are, sooner or later you will get conflicts within your dependencies. Maybe you're cloning an old project or maybe you just updated your environment and your `node_modules` folder must be regenerated.

Sometimes after running `npm install` you don't get exactly the modules that you need and suddenly things don't work as expected anymore.

This might happen when there's [no specific version](https://docs.npmjs.com/misc/semver) defined of some dependencies in your `package.json`, but rather an approximate one (e.g.: using a tilde, as in `~1.2.3` or a caret, as in `^1.2.3`).

While tildes and carets can be useful if you want to automatically use more recent versions when available, they can sometimes break your project if anything new isn't compatible with your current code.

By just specifying a version number without carets and tildes (as in `1.2.3`), you fix the specific version of each dependency you need.

### Lock down dependency versions

Sometimes using specific module versions is not enough. Saving a list with the specific versions of all your dependencies might save your day.

Use the [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap) command in order to create an `npm-shrinkwrap.json` file that [locks](https://docs.npmjs.com/files/package-locks) all the npm dependencies:

`npm shrinkwrap`

Another (even better) alternative is to use [Yarn](https://yarnpkg.com/en/docs/yarn-lock) to create a `yarn.lock` file, which has the same goal as the `npm-shrinkwrap.json`, but it seems to follow a more deterministic approach, being therefore safer.

Yarn is compatible with the same `package.json` of npm, so you can just install it globally (`npm install -g yarn`) and try it out right away by simply running `yarn` on your project's root folder, which will generate the `yarn.lock`.

It a very good idea to add this `yarn.lock` to your version control.

## Remove unneeded dependencies

If you want to remove modules from your `node_modules` directory that you once installed, but that are not required anymore by your project, you can use [prune](https://docs.npmjs.com/cli/prune):

`npm prune`

`npm prune --production` will also remove the *devDependencies*.

This is particularly useful before running `npm shrinkwrap` if you manually removed dependencies from your `package.json`.

## List outdated dependencies

If you want to check out which dependencies within your project are outdated, you can use the [npm-outdated](https://docs.npmjs.com/cli/outdated) command:

`npm outdated`

## Update dependencies

With the module `npm-check-updates`, you can automatically update your dependencies to the latest versions (be careful when doing this, since modifying various depencencies at once might break things down).

- `npm i -g npm-check-updates` Install the [npm-check-updates](https://www.npmjs.com/package/npm-check-updates) module globally
- `npm-check-updates -u` the option -u upgrades your `package.json` automatically
- `npm install` in order to install the new versions of the dependencies (you might need to remove the `node_modules` directory first)

## Some other useful commands

- `npm ls` list the installed packages
- `npm dedupe` reduce duplication

