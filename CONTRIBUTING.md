# Contributing to LeApp

:+1::tada: First off, thanks for taking the time to contribute! :tada::+1:

The following is a set of guidelines for contributing effectively to LeApp,
which is hosted in the [LeApp-To Organization](https://github.com/leapp-to/) on Github.
These guidelines are expected to change over time as the needs of the project
evolve, so feel free to propose changes to this document in a pull request -
it's a lot easier to discuss a specific proposed update than it is general
principles.

## Submitting Issues

* All elements of the current prototype (documentation, CLI, demonstration
Cockpit plugin, integration tests) are maintained in the
[prototype repo](https://github.com/leapp-to/prototype), so any issues
should be filed there regardless of the component they relate to

## Submitting a Pull Request

*Note: Every PR should have at least one review from at least one of the Core Reviewers.*

Core Reviewers are:

* Nick Coghlan
* Vinzenz Feenstra
* Marcin Franczyk
* Veaceslav Mindru
* Pavel Odvody

Before you submit your pull request consider the following guidelines:

* Fork the repository and clone your fork
* Make your changes in a new git branch:

     ```shell
     git checkout -b bug/my-fix-branch master
     ```

* Create your patch, **ideally including appropriate test cases**
* Include documentation that either describe a change to a behavior or the changed capability to an end user
* Commit your changes using **a descriptive commit message**. If you are fixing an issue please include something like 'this closes issue #xyz'
* Make sure your tests pass! We use Jenkins CI for our automated testing
* Push your branch to GitHub:

    ```shell
    git push origin bug/my-fix-branch
    ```

* When opening a pull request, select the `master` branch as a base.
* Mark your pull request with **[WIP]** (Work In Progress) to get feedback but prevent merging (e.g. [WIP] Update CONTRIBUTING.md)
* If we suggest changes then:
  * Make the required updates
  * Push changes to git (this will update your Pull Request):
    * You can add new commit
    * Or rebase your branch and force push to your Github repository:

    ```shell
    git rebase -i master
    git push -f origin bug/my-fix-branch
    ```

That's it! Thank you for your contribution!

### Merge Rules

* Include unit or integration tests for the capability you have implemented
* Include documentation for the capability you have implemented
* If you are fixing an issue, include the issue number you are fixing
* Python code should follow [PEP8](https://www.python.org/dev/peps/pep-0008/) conventions

## Git Commit Messages

* Use the present tense ("Add feature" not "Added feature")
* Use the imperative mood ("Move cursor to..." not "Moves cursor to...")
* Reference issues and pull requests liberally

## Component specific details

### `leapp-tool` CLI

* The `leapp-tool` CLI is written in the Python 3 compatible subset of Python 2.7
* All CLI dependencies must be available and installable as RPMs
* COPR repositories will be used for any dependencies not available by default
  in RHEL/CentOS
* Use `sudo` for operations that must be executed with elevated privileges
* The integration tests install and run the CLI under Python 2.7 on CentOS 7,
  but don't currently check Python 3 compatibility

### Integration tests

* The integration tests run under Python 3.5 using the `behave` framework
* Integration testing VMs are managed using Vagrant and configured via Ansible
* On CentOS/RHEL 7, the integration tests require the Vagrant and Python 3.5
  SCLs
* Test dependencies may be installed as RPMs, Vagrant plugins, or through
  `pipenv` (i.e. Python packages)
* All test dependencies should be pinned to a specific version, so changes
  aren't unexpectedly introduced into the CI environment
* When new feature files, step files, or context attributes are defined, add
  a corresponding entry to the
  [integration tests README](../integration-tests/README.md)
* see the [integration tests README](../integration-tests/README.md) for more
  details

### Demonstration Cockpit plugin

* The demonstration Cockpit plugin is currently written in JavaScript & raw HTML
* It should run correctly when used in Firefox on RHEL/CentOS 7 (this
  requirement is not currently checked in the CI environment, but can be
  tested locally by running `behave --wip`)
