---
title: Node Function Manager | Home
curbook: home
---

#### Welcome to the Node Function Manager or NfuncM for short

> Share Functions not Modules!

---

#### What is NfuncM?

Have you ever thought there needed to be a middle ground between a snippet of code on Stack Overflow and a full blown NPM Module?

Have you ever seen an NPM module and thought to yourself "This really shouldn't be a module." or "It's just a couple lines of code"?

Personally, I've thought this myself many times, but came the conclusion, that receiving updates to this short valuable code is something that can't be missed.

But with the days of the JavaScript ecosystem moving fast and breaking things, and megabytes of dependencies for any web app, NfuncM returns to a simpler workflow.

One were working together in the Open Source community is still valued, but so is simplicity, small install sizes, and non-breaking changes.

This is what the Node Function Manager aims to achieve.

#### Why NfuncM?

The concept behind NfuncM is that if your application needs some snippet of code, say a regex to check the validity of an email address.

Currently there are three major ways to solve this:

1. Write it yourself, perfectly valid, but not for everyone.
2. Install a module on NPM that does this for you. Being aware that this module may add to your install size unnecessarily, or may experience breaking changes that you must keep up with at all times.
3. Search online, likely StackOverflow, for someone else's solution. Once you've found this answer on StackOverflow you hit copy and paste, and be done with it.

Continuing on this example, lets assume you've taken the answer from StackOverflow.

There are now to big concerns:

1. Most users don't even realize that code snippets have a license on StackOverflow that should be respected. And unless you've done your research you likely aren't either.
2. What if six months from now the original author realizes there was a mistake, or the RFC changes, and this regex is updated. You have no way to know, and likely will never look at that answer again, until a user reports an issue.

With NfuncM, ideally we can avoid these issues.

#### How does NfuncM Work?

With NfuncM, anyone can publish a function, which 'publish' is being used loosely here; there is no centralized repository for functions, a function can be installed from any possible URL that returns JavaScript. Once published a user can then install this function, which using our earlier example looks like this:

```shell
nfm install emailRegex https://codesite.com/awesome-user/emailRegex/source.js
```

From here, NfuncM will register this installation in your `package.json`, which is used similar to NPM's or Yarn's lock files, and saves this module into a folder titled `nfm` in your project.

Now within your code you can simply use the following to access this function:

```javascript
const { emailRegex } = require("./nfm");
```

That's it.

You are now free to open up the `nfm` folder in your project and figure out exactly how this function works without digging into hundreds of `node_modules`.

Additionally, since this is a single file of such small size, it's easy to include this into a GitHub repository if you'd rather not ask all maintainers to install NfuncM, or it can be easily included within a Docker image to reduce a build step.

And since all function publishers are highly encouraged to keep licensing information within their function you rarely need to go digging for this information.

#### Okay but really, why should I use NfuncM and not NPM?

The honest answer, is this isn't always the case. Some snippets of code **absolutely** should be an NPM module. Where you are able to install dependencies, have hundreds of files, and use whatever post install or transpilation steps needed. But on the flip side, many modules consist of no dependencies and under 50 lines of code. This solution is focused precisely on this modules.

But when using NfuncM there are many bonus' even if not all unique:

* Snippets are still able to receive updates: You don't need to hunt on StackOverflow for updates to a comment you used years later.
* Authors Licenses are respected: Meanwhile currently many users don't realize StackOverflow snippets require licensing at all.
* Less Disk Space: Due to the limitations placed on functions, you'll no longer find hundreds of dependencies being installed by your one.
* Simplicity: If configured correctly, there's no transpiling steps needed, to install commands needed, or complex hoisting logic involved in accessing files that are as local as your code itself.

Beyond this, the NfuncM package itself is unobtrusive and as simple as possible:
* NfuncM has **zero** dependencies
* NfuncM is only 12.9 kB unpacked

The goal of NfuncM is to simplify a portion of software development, and respect the authors of helpful snippets as much as possible.

#### How do I get started?

To start using NfuncM, it's easy!

Firstly, install NfuncM to your system:

```shell
npm install nfuncm/nfuncm
```

Then use:

```shell
> nfm help
>
NFM: Node Function Manager
Allows installation of individual functions to access in JavaScript applications.

Usage: nfm <command>

nfm install               Install all functions into your project
nfm install <foo> <url>   Add the function <foo> to your project with the URL <url>
nfm uninstall <foo>       Remove the function <foo> from your project
nfm help                  Display this help message
>
```

Keep in mind how simple the install command really is:

* The name of the function can be whatever you want, obviously recommended to match the name of the function, but this has lots of freedom to be anything, to ensure there are never collisions, either in your code or with another function.
* The URL is just that. Any URL that can be accessed via `http` or `https`

#### How do Configure NfuncM

Any and all configuration of NfuncM is done via your function/package's `package.json`, where everything should be managed under and `nfm` top level key.

The following options are valid within this key:

* `installed`: This is an object of all functions installed to your code. Where the key to each object is the custom name assigned during installation, and the value is the raw link to install from.
* `minify`: This value is used by [`nfuncm-packager`](https://github.com/nfuncm/nfuncm-packager) which controls how or if minification is used on your function. More details are available in that package's [configuration](https://github.com/nfuncm/nfuncm-packager#configuration).
* `useHttp`: Can be used if for any reason HTTP should be forced for all function installation. Such as if you receive SSL errors, or are behind a proxy.

#### Developing a Function

Developing a function is made to be as easy as possible, although there are some hard rules, and some best practices that should be following.

But first the basics of a function:

A function is really any single file that uses CommonJS to export any utility for other code to use. This can be anything, from exporting a `const` of complicated `Regex`, or a simple function to check the type of a value, or executes a mathematical operation, or anything else you can dream up!

But like mentioned, there are some limits to what can be done:

* A function, when installed by an end user, **must** be a single file. Now without any fancy CI steps, this means the function should consist of a single file only. Not using any NPM dependencies, submodules, or even other functions.
* There is intentionally zero support of any post installation steps, meaning any transpiling or converting from TypeScript or CoffeeScript must happen in your CI environment, leaving the user with a single file to install.

But beyond that it's good to know the best practices to attempt to give everyone the best experience:

* Scope: It's recommended to keep the scope of any given function small. As the function should attempt to be simplistic and a 'single step' of code, rather than the whole workflow.
* Dependencies: Like mentioned above there's intentionally zero support for dependencies of any kind. Although some build tools undoubtedly could combine several files into one, it's suggested that if you find yourself needing many dependencies, then your code may be better suited for an NPM module. Whereas code best suited to be a function generally should be able to be accomplished without any dependencies.
* Breaking Changes: You may notice that there's a significant lack of any versioning mentioned or supported in NfuncM. This is intentional, NfuncM doesn't intend to replace package managers supporting different versions or pre-release channels of code. It's intended to provide a single function with simple interfaces. It's recommended to provide a link to functions that will continue to get improvements to users without any action on their part (This means it's best to **not** provide a link to your code that's static and will never change, such as on a specific tag of your repo, as they will then never see any updates). If your function does need breaking changes to improve, it's recommended to do this either in a way where your function can accept either the old style of usage, or the new to support any version, or that the new version is itself a brand new function. Leaving the old one still available.
