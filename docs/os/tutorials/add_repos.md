## What is a Repository

A repository is a version-ed mynewt project, which is a 
collection of mynewt packages organized in a specific
way.  

What differentiates a repository from a mynewt project is the presence of a
`repository.yml` file describing the repository. This will be described 
below.

For the remainder of this document we'll use the term repo as shorthand for a 
mynewt repository.

Repos are useful because they are an organized way for the community
to share mynewt packages and projects.  In fact, the mynewt-core is 
distributed as a repo.

# Additional Repos 

Why does mynewt need additional repos? 

Repos add functionality 
not included in the mynewt core.  New repos might be created for 
several reasons.

* **Expertise**.  Individuals or organizations may have expertise that they want
to share in the form of repos. For example a chip vendor may
create a repo to hold the mynewt support for their chips.
* **Non-Core component**.  Some components, although very useful to Mynewt users
are not core to all mynewt users.  These are likely candidate to be held in 
different repos.
* **Software licensing**.  Some software has licenses that make them incompatible
with the ASF (Apache Software Foundation) license policies.  These may be 
valuable components to some mynewt users, but cannot be contained in the `apache-mynewt-core`.

## What Repos are in my Project

The list of repos used by your project are contained within the 
`project.yml` file.  An example can be seen by creating a new project:

```no-highlight
$ mkdir ~/dev
$ cd ~/dev
$ newt new myproj
$ cd myproj
```

View the `project.yml` section and you will see a line describing the repos:

```no-highlight
project.repositories:
    - apache-mynewt-core
```

By default, this newly created project uses a single repo called 
`apache-mynewt-core`.  

If you wish to add additional repos, you would add 
additional lines to the `project.repositories` variable like this.

```no-highlight
project.repositories:
    - apache-mynewt-core
    - another_repo_named_x
```

## Repo Descriptors

In addition to the repo name, the `project.yml` file must also contain
a repo descriptor for each repository you include that gives `newt` 
information on obtaining the repo.

In the same `myproj` above you will see the following repo descriptor.
 
```no-highlight
repository.apache-mynewt-core:
    type: github
    vers: 0-latest
    user: apache
    repo: incubator-mynewt-core
```

A repo descriptor starts with `repository.<name>.`.  In this example, the 
descriptor specifies the information for the `apache-mynewt-core`.

The fields within the descriptor have the following definitions:

* **type** -- The type of code storage the repo uses.  The current version
of `newt` only supports github.  Future versions may support generic git or other
code storage mechanisms.

* **vers** -- The version of the repo to use for your project.  A source
code repository contains many version of the source. This field is used to 
specify the one to use for this project.  See the section on versions below 
for a detailed description of the format of this field.

* **user** -- The username for the repo.  On github, this is the name
after `github.com` in the repo path.  Consider the repository 
`https://github.com/apache/incubator-mynewt-core`. It has username `apache`.  

* **repo** -- The name of the repo.  On github, this is the name after
the user name described above.  Consider the repository 
`https://github.com/apache/incubator-mynewt-core`. It has username 
`incubator-mynewt-core`.  This is a path to the source control
and should not be confused with the name of the repo that you used in the 
`repository.<name>` declaration above.   That name is contained elsewhere
within the repo. See Below.

## Adding Existing Repos to my Project

To add a new repo to your project, you have to complete two steps.

*  Edit the `project.yml` file and add a new repo descriptor.  The previous
section includes information on the field required in your repo descriptor.

* Edit the `project/yml` file and add a new line to the `project.repositories`
variable with the name of the repo you are adding.  

An example if a `project.yml` file with two repositories is shown below:

```no-highlight
project.name: "my_project"

project.repositories:
    - apache-mynewt-core
    - mynewt_arduino_zero

# Use github's distribution mechanism for core ASF libraries.
# This provides mirroring automatically for us.
#
repository.apache-mynewt-core:
    type: github
    vers: 0-latest
    user: apache
    repo: incubator-mynewt-core

# a special repo to hold hardware specific stuff for arduino zero
repository.mynewt_arduino_zero:
    type: github
    vers: 0-latest
    user: runtimeinc
    repo: mynewt_arduino_zero
```

## What Version of the Repo

Mynewt repos are version-ed artifacts.  They are stored in source control 
systems like github.  The repo descriptor in your `project.yml` file must
specify the version of the repo you will accept into your project.

For now, we are at the beginnings of mynewt. For testing and evaluation
please use `0-latest` in the `vers` field in your repo descriptor.

```
    vers:0-latest
```

See below for a description of the version system.

## What is in a Repo

A repo is a collection of mynewt packages organized in a specific
way and stored in one of the supported code storage methods described above.  

A repo is a mynewt project, with an additional file `repository.yml`
which describes the repo for `newt` (and humans browsing them).

There is one key consideration for this `repository.yml` file. The 
`repository.yml` file only lives in the master branch of the git
repository.  `Newt` will always fetch this file from the master branch and then
use that to resolve the actual branch required depending on the version
specified in the project.  Special care should be taken to ensure that this
file exists only in the master branch.

Here is the `repository.yml` file from the apache-mynewt-core 

```no-highlight
repo.name: apache-mynewt-core
repo.versions:
    "0.7.9": "mynewt_0_8_0_b2_tag"
    "0-latest": "0.7.9"
    "0.8-latest": "0.7.9"
```

it contains the following:

* **repo.name** The external name that is used to include the library in 
your `project.yml` file.   This is the name you in include in the `project.repositories` variable when adding this repository to your project.
* **repo.versions** A description of what versions to give the user depending 
on the settings in their `project.yml` file.  See below for a thorough description
on versioning. Its a flexible mapping between version numbers and git branches.

## Repo Version

The version field argument in your `project.yml` file has the following format:

```no-highlight
<major_num>.<minor_num>.<revision_num>
```

or

```no-highlight
<major_num>.<minor_num>-<stability string>
```

or 

```no-highlight
<major_num>-<stability string>
```

The stability string can be one of 3 pre-defined stability values.

1. stable -- A stable release version of the repository
2. dev    -- A development version from the repository
3. latest -- The latest from the repository

In your `project.yml` file you can specify different combinations of 
the version number and stability value.  For example:

* `0-latest`      -- The latest version with major number 0
* `1.2-stable`    -- The latest stable version with major and minor number 1.2
* `1.2-dev`       -- The development version from 1.2
* `1.1.1`         -- a specific version 1.1.1

You cannot specify a stability string with a fully number version, e.g.

```no-highlight
1.2.8-stable
```

A `repository.yml` file contains information to match this version request
into a git branch to fetch for your project.

Its up to the repository maintainer to map these to branches of the 
repository.  For example, in a fictitious repository, the following are 
defined.

```no-highlight
repo.versions:
    "0.8.0": "xxx_branch_0_8_0"
    "1.0.0": "xxx_branch_1_0_0"
    "1.0.2": "xxx_branch_1_0_2"
    "1.1.1": "xxx_branch_1_1_0"
    "1.1.2": "xxx_branch_1_1_2"
    "1.2.0": "xxx_branch_1_2_0"
    "1.2.1": "xxx_branch_1_2_1"
    "1.2-dev": "1.2.1"
    "1-dev": "1.2-dev"
    "1.2-stable": "1.2.0"
    "0-latest": "0.8.0"
    "1-latest": "1-dev"
    ....
```

When the `project.yml` file asks for `1.2-stable` its resolved to version
`1.2.0` (perhaps `1.2.1` is not stable yet), which resolves to a specific
branch `xxx_branch_1_2_0`.  This is the branch that `newt` fetches into 
your project.

## How do I find out what Repos are available for Mynewt components

Currently, there is no `newt` command to locate/search mynewt package 
repositories.  However, since the `newt` tool supports only github, 
searching github by keyword is a satisfactory option until a search 
tool is created.

When searching github, recall that a mynewt repository must 
have a `repository.yml` file in its root directory. If you don't see 
that file, its not a mynewt repository can cant be included in your 
project via the mynewt tool.  

Once you find a repository, the github URL and `repository.yml` file
should give you all the information to add it to your `project.yml` file.







