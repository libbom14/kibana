# Contributing to Kibana

We understand that you may not have days at a time to work on Kibana. We ask that you read our contributing guidelines carefully so that you spend less time, overall, struggling to push your PR through our code review processes.

At the same time, reading the contributing guidelines will give you a better idea of how to post meaningful issues that will be more easily be parsed, considered, and resolved. A big win for everyone involved! :tada:

## Table of Contents

A high level overview of our contributing guidelines.

- [Effective issue reporting in Kibana](#effective-issue-reporting-in-kibana)
  - [Voicing the importance of an issue](#voicing-the-importance-of-an-issue)
  - ["My issue isn't getting enough attention"](#my-issue-isnt-getting-enough-attention)
  - ["I want to help!"](#i-want-to-help)
- [How We Use Git and GitHub](#how-we-use-git-and-github)
  - [Branching](#branching)
  - [Commits and Merging](#commits-and-merging)
  - [What Goes Into a Pull Request](#what-goes-into-a-pull-request)
- [Contributing Code](#contributing-code)
  - [Setting Up Your Development Environment](#setting-up-your-development-environment)
    - [Customizing `config/kibana.dev.yml`](#customizing-configkibanadevyml)
    - [Setting Up SSL](#setting-up-ssl)
  - [Linting](#linting)
  - [Testing and Building](#testing-and-building)
  - [Debugging Unit Tests](#debugging-unit-tests)
  - [Unit Testing Plugins](#unit-testing-plugins)
  - [Cross-browser compatibility](#cross-browser-compatibility)
    - [Testing compatibility locally](#testing-compatibility-locally)
    - [Running Browser Automation Tests](#running-browser-automation-tests)
      - [Browser Automation Notes](#browser-automation-notes)
  - [Building OS packages](#building-os-packages)
  - [Writing documentation](#writing-documentation)
- [Signing the contributor license agreement](#signing-the-contributor-license-agreement)
- [Submitting a Pull Request](#submitting-a-pull-request)
- [Code Reviewing](#code-reviewing)
  - [Getting to the Code Review Stage](#getting-to-the-code-review-stage)
  - [Reviewing Pull Requests](#reviewing-pull-requests)

Don't fret, it's not as daunting as the table of contents makes it out to be!

### Voicing the importance of an issue

We seriously appreciate thoughtful comments. If an issue is important to you, add a comment with a solid write up of your use case and explain why it's so important. Please avoid posting comments comprised solely of a thumbs up emoji 👍.

Granted that you share your thoughts, we might even be able to come up with creative solutions to your specific problem. If everything you'd like to say has already been brought up but you'd still like to add a token of support, feel free to add a [👍 thumbs up reaction](https://github.com/blog/2119-add-reactions-to-pull-requests-issues-and-comments) on the issue itself and on the comment which best summarizes your thoughts.

### "My issue isn't getting enough attention"

First of all, **sorry about that!** We want you to have a great time with Kibana.

Hosting meaningful discussions on GitHub can be challenging. For that reason, we'll sometimes ask that you join us on IRC _([#kibana](https://kiwiirc.com/client/irc.freenode.net/?#kibana) on freenode)_ to chat about your issues. You may also experience **faster response times** when engaging us via IRC.

There's hundreds of open issues and prioritizing what to work on is an important aspect of our daily jobs. We prioritize issues according to impact and difficulty, so some issues can be neglected while we work on more pressing issues.

Feel free to bump your issues if you think they've been neglected for a prolonged period, or just jump on IRC and let us have it!

### "I want to help!"

**Now we're talking**. If you have a bug fix or new feature that you would like to contribute to Kibana, please **find or open an issue about it before you start working on it.** Talk about what you would like to do. It may be that somebody is already working on it, or that there are particular issues that you should know about before implementing the change.

We enjoy working with contributors to get their code accepted. There are many approaches to fixing a problem and it is important to find the best approach before writing too much code.

## How We Use Git and GitHub

### Forking

We follow the [GitHub forking model](https://help.github.com/articles/fork-a-repo/) for collaborating
on Kibana code. This model assumes that you have a remote called `upstream` which points to the
official Kibana repo, which we'll refer to in later code snippets.

### Branching

* All work on the next major release goes into master.
* Past major release branches are named `{majorVersion}.x`. They contain work that will go into the next minor release. For example, if the next minor release is `5.2.0`, work for it should go into the `5.x` branch.
* Past minor release branches are named `{majorVersion}.{minorVersion}`. They contain work that will go into the next patch release. For example, if the next patch release is `5.3.1`, work for it should go into the `5.3` branch.
* All work is done on feature branches and merged into one of these branches.
* Where appropriate, we'll backport changes into older release branches.

### Commits and Merging

* Feel free to make as many commits as you want, while working on a branch.
* When submitting a PR for review, please perform an interactive rebase to present a logical history that's easy for the reviewers to follow.
* Please use your commit messages to include helpful information on your changes, e.g. changes to APIs, UX changes, bugs fixed, and an explanation of *why* you made the changes that you did.
* Resolve merge conflicts by rebasing the target branch over your feature branch, and force-pushing (see below for instructions).
* When merging, we'll squash your commits into a single commit.

#### Rebasing and fixing merge conflicts

Rebasing can be tricky, and fixing merge conflicts can be even trickier because it involves force pushing. This is all compounded by the fact that attempting to push a rebased branch remotely will be rejected by git, and you'll be prompted to do a `pull`, which is not at all what you should do (this will really mess up your branch's history).

Here's how you should rebase master onto your branch, and how to fix merge conflicts when they arise.

First, make sure master is up-to-date.

```
git checkout master
git fetch upstream
git rebase upstream/master
```

Then, check out your branch and rebase master on top of it, which will apply all of the new commits on master to your branch, and then apply all of your branch's new commits after that.

```
git checkout name-of-your-branch
git rebase master
```

You want to make sure there are no merge conflicts. If there are merge conflicts, git will pause the rebase and allow you to fix the conflicts before continuing.

You can use `git status` to see which files contain conflicts. They'll be the ones that aren't staged for commit. Open those files, and look for where git has marked the conflicts. Resolve the conflicts so that the changes you want to make to the code have been incorporated in a way that doesn't destroy work that's been done in master. Refer to master's commit history on GitHub if you need to gain a better understanding of how code is conflicting and how best to resolve it.

Once you've resolved all of the merge conflicts, use `git add -A` to stage them to be commiteed, and then use `git rebase --continue` to tell git to continue the rebase.

When the rebase has completed, you will need to force push your branch because the history is now completely different than what's on the remote. **This is potentially dangerous** because it will completely overwrite what you have on the remote, so you need to be sure that you haven't lost any work when resolving merge conflicts. (If there weren't any merge conflicts, then you can force push without having to worry about this.)

```
git push origin name-of-your-branch --force
```

This will overwrite the remote branch with what you have locally. You're done!

**Note that you should not run `git pull`**, for example in response to a push rejection like this:

```
! [rejected] name-of-your-branch -> name-of-your-branch (non-fast-forward)
error: failed to push some refs to 'https://github.com/YourGitHubHandle/kibana.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

Assuming you've successfully rebased and you're happy with the code, you should force push instead.

### What Goes Into a Pull Request

* Please include an explanation of your changes in your PR description.
* Links to relevant issues, external resources, or related PRs are very important and useful.
* Please update any tests that pertain to your code, and add new tests where appropriate.
* See [Submitting a Pull Request](#submitting-a-pull-request) for more info.

## Contributing Code

These guidelines will help you get your Pull Request into shape so that a code review can start as soon as possible.

### Setting Up Your Development Environment

Fork, then clone the `kibana` repo and change directory into it

```bash
git clone https://github.com/[YOUR_USERNAME]/kibana.git kibana
cd kibana
```

Install the version of node.js listed in the `.node-version` file _(this can be easily automated with tools such as [nvm](https://github.com/creationix/nvm) and [avn](https://github.com/wbyoung/avn))_

```bash
nvm install "$(cat .node-version)"
```

Install `npm` dependencies

```bash
npm install
```

Start elasticsearch.

```bash
npm run elasticsearch
```

> You'll need to have a `java` binary in `PATH` or set `JAVA_HOME`.

If you're just getting started with `elasticsearch`, you could use the following command to populate your instance with a few fake logs to hit the ground running.

```bash
node scripts/makelogs
```

> Make sure to execute `node scripts/makelogs` *after* elasticsearch is up and running!

Start the development server.
  ```bash
  npm start
  ```

> On Windows, you'll need you use Git Bash, Cygwin, or a similar shell that exposes the `sh` command.  And to successfully build you'll need Cygwin optional packages zip, tar, and shasum.

Now you can point your web browser to https://localhost:5601 and start using Kibana! When running `npm start`, Kibana will also log that it is listening on port 5603 due to the base path proxy, but you should still access Kibana on port 5601.

#### Customizing `config/kibana.dev.yml`

The `config/kibana.yml` file stores user configuration directives. Since this file is checked into source control, however, developer preferences can't be saved without the risk of accidentally committing the modified version. To make customizing configuration easier during development, the Kibana CLI will look for a `config/kibana.dev.yml` file if run with the `--dev` flag. This file behaves just like the non-dev version and accepts any of the [standard settings](https://www.elastic.co/guide/en/kibana/current/settings.html).

#### Potential Optimization Pitfalls

In development mode, Kibana runs a customized version of [Webpack](http://webpack.github.io/) with some optimizations enabled to make building the browser bundles as fast as possible. These optimizations make the build process about 2x as fast for initial builds, and about 7x faster for rebuilds, but are labeled "unsafe" by Webpack because they can sometimes cause changes to go unnoticed by the compiler. If you experience any of the scenarios below either restart the dev server, or add `optimize.unsafeCache: false` to your `config/kibana.dev.yml` file to disable these optimizations completely.

 - Webpack is trying to include a file in the bundle that I deleted and is now complaining about it is missing
 - A module id that used to resolve to a single file now resolves to a directory, but webpack isn't adapting
 - (if you discover other scenarios, please send a PR!)

#### Setting Up SSL

Kibana includes a self-signed certificate that can be used for development purposes: `npm start -- --ssl`.

### Linting

A note about linting: We use [eslint](http://eslint.org) to check that the [styleguide](STYLEGUIDE.md) is being followed. It runs in a pre-commit hook and as a part of the tests, but most contributors integrate it with their code editors for real-time feedback.

Here are some hints for getting eslint setup in your favorite editor:

Editor     | Plugin
-----------|-------------------------------------------------------------------------------
Sublime    | [SublimeLinter-eslint](https://github.com/roadhump/SublimeLinter-eslint#installation)
Atom       | [linter-eslint](https://github.com/AtomLinter/linter-eslint#installation)
VSCode     | [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
IntelliJ   | Settings » Languages & Frameworks » JavaScript » Code Quality Tools » ESLint
`vi`       | [scrooloose/syntastic](https://github.com/scrooloose/syntastic)

Another tool we use for enforcing consistent coding style is EditorConfig, which can be set up by installing a plugin in your editor that dynamically updates its configuration. Take a look at the [EditorConfig](http://editorconfig.org/#download) site to find a plugin for your editor, and browse our [`.editorconfig`](https://github.com/elastic/kibana/blob/master/.editorconfig) file to see what config rules we set up.

### Testing and Building

To ensure that your changes will not break other functionality, please run the test suite and build process before submitting your Pull Request.

Before running the tests you will need to install the projects dependencies as described above.

Once that's done, just run:

```bash
npm run test && npm run build -- --skip-os-packages
```

### Debugging Unit Tests

The standard `npm run test` task runs several sub tasks and can take several minutes to complete, making debugging failures pretty painful. In order to ease the pain specialized tasks provide alternate methods for running the tests.

To execute both server and browser tests, but skip linting, use `npm run test:quick`.

```bash
npm run test:quick
```

Use `npm run test:server` when you want to run only the server tests.

```bash
npm run test:server
```

When you'd like to execute individual server-side test files, you can use the command below. Note that this command takes care of configuring Mocha with Babel compilation for you, and you'll be better off avoiding a globally installed `mocha` package. This command is great for development and for quickly identifying bugs.

```bash
node scripts/mocha <file>
```

You could also add the `--debug` option so that `node` is run using the `--debug-brk` flag. You'll need to connect a remote debugger such as [`node-inspector`](https://github.com/node-inspector/node-inspector) to proceed in this mode.

```bash
node scripts/mocha --debug <file>
```

With `npm run test:browser`, you can run only the browser tests. Coverage reports are available for browser tests by running `npm run test:coverage`. You can find the results under the `coverage/` directory that will be created upon completion.

```bash
npm run test:browser
```

Using `npm run test:dev` initializes an environment for debugging the browser tests. Includes an dedicated instance of the kibana server for building the test bundle, and a karma server. When running this task the build is optimized for the first time and then a karma-owned instance of the browser is opened. Click the "debug" button to open a new tab that executes the unit tests.

```bash
npm run test:dev
```

In the screenshot below, you'll notice the URL is `localhost:9876/debug.html`. You can append a `grep` query parameter to this URL and set it to a string value which will be used to exclude tests which don't match. For example, if you changed the URL to `localhost:9876/debug.html?query=my test` and then refreshed the browser, you'd only see tests run which contain "my test" in the test description.


![Browser test debugging](http://i.imgur.com/DwHxgfq.png)

### Unit Testing Plugins

This should work super if you're using the [Kibana plugin generator](https://github.com/elastic/generator-kibana-plugin). If you're not using the generator, well, you're on your own. We suggest you look at how the generator works.

To run the tests for just your particular plugin run the following command from your plugin:

```bash
npm run test:server
npm run test:browser -- --dev # remove the --dev flag to run them once and close
```

### Cross-browser Compatibility

#### Testing Compatibility Locally

##### Testing IE on OS X

* [Download VMWare Fusion](http://www.vmware.com/products/fusion/fusion-evaluation.html).
* [Download IE virtual machines](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/#downloads) for VMWare.
* Open VMWare and go to Window > Virtual Machine Library. Unzip the virtual machine and drag the .vmx file into your Virtual Machine Library.
* Right-click on the virtual machine you just added to your library and select "Snapshots...", and then click the "Take" button in the modal that opens. You can roll back to this snapshot when the VM expires in 90 days.
* In System Preferences > Sharing, change your computer name to be something simple, e.g. "computer".
* Run Kibana with `npm start -- --host=computer.local` (substituting your computer name).
* Now you can run your VM, open the browser, and navigate to `http://computer.local:5601` to test Kibana.

#### Running Browser Automation Tests

[Read about the `FunctionalTestRunner`](https://www.elastic.co/guide/en/kibana/current/development-functional-tests.html) to learn more about how you can run and develop functional tests for Kibana core and plugins.


### Building OS packages

Packages are built using fpm, pleaserun, dpkg, and rpm.  fpm and pleaserun can be installed using gem.  Package building has only been tested on Linux and is not supported on any other platform.

```bash
apt-get install ruby-dev rpm
gem install fpm -v 1.5.0
gem install pleaserun -v 0.0.24
npm run build -- --skip-archives
```

To specify a package to build you can add `rpm` or `deb` as an argument.

```bash
npm run build -- --rpm
```

Distributable packages can be found in `target/` after the build completes.

### Writing documentation

Kibana documentation is written in [asciidoc](http://asciidoc.org/) format in
the `docs/` directory.

To build the docs, you must clone the [elastic/docs](https://github.com/elastic/docs)
repo as a sibling of your kibana repo. Follow the instructions in that project's
README for getting the docs tooling set up.

**To build the docs and open them in your browser:**

```bash
node scripts/docs.js --open
```

## Signing the contributor license agreement

Please make sure you have signed the [Contributor License Agreement](http://www.elastic.co/contributor-agreement/). We are not asking you to assign copyright to us, but to give us the right to distribute your code without restriction. We ask this of all contributors in order to assure our users of the origin and continuing existence of the code. You only need to sign the CLA once.

## Submitting a Pull Request

Push your local changes to your forked copy of the repository and submit a Pull Request. In the Pull Request, describe what your changes do and mention the number of the issue where discussion has taken place, eg “Closes #123″.

Always submit your pull against `master` unless the bug is only present in an older version. If the bug effects both `master` and another branch say so in your pull.

Then sit back and wait. There will probably be discussion about the Pull Request and, if any changes are needed, we'll work with you to get your Pull Request merged into Kibana.

## Code Reviewing

After a pull is submitted, it needs to get to review. If you have commit permission on the Kibana repo you will probably perform these steps while submitting your Pull Request. If not, a member of the Elastic organization will do them for you, though you can help by suggesting a reviewer for your changes if you've interacted with someone while working on the issue.

### Getting to the Code Review Stage

1. Assign the `review` label. This signals to the team that someone needs to give this attention.
1. Do **not** assign a version label. Someone from Elastic staff will assign a version label, if necessary, when your Pull Request is ready to be merged.
1. Find someone to review your pull. Don't just pick any yahoo, pick the right person. The right person might be the original reporter of the issue, but it might also be the person most familiar with the code you've changed. If neither of those things apply, or your change is small in scope, try to find someone on the Kibana team without a ton of existing reviews on their plate. As a rule, most pulls will require 2 reviewers, but the first reviewer will pick the 2nd.

### Reviewing Pull Requests

So, you've been assigned a pull to review. Check out our [pull request review guidelines](https://www.elastic.co/guide/en/kibana/master/pr-review.html) for our general philosophy for pull request reviewers.

Thank you so much for reading our guidelines! :tada:
