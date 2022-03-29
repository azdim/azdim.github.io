---
title: "Better code commits"
description: "It is important to have tested code as well as clear commit messages."
date: 2022-03-28
images: ["avatar2.jpg"]
tags: ["git"]
---

## Introduction

As a DevOps/Cloud engineer git is a core tool in our arsenal. A git commit is an action that we execute daily, hourly and sometimes even shorter. I do hope that you the reader are not committing code every second, that would be shocking. Committing code not only creates a snapshot of the work you have done but also describes the work you have done in a commit message.

```bash
git add .

git commit -m "It works"
```

By the end of this post I sincerely hope that the two commands above are not the only ones you will run prior to pushing code. And in the case of the second command, well it is trash.

## Why the fuss?

Git provides a [decentralised workflow](https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows), where you as an engineer contribute to a repository that others also contribute to. It is therefore important that a commit provides a clear insight to changes that have been made for quicker debugging (time is money). It is also important that we test our code for linting issues. Your code doesn't need to work but it needs to look good. Errors are meant to happen.

These days CI/CD pipelines are triggered on git pushes and so it is important to do everything you can to prevent a failure in the pipeline.

## So how do I make my code better?

Here I will introduce two tools

1. [pre-commit](https://pre-commit.com): This tool will be used for code testing
2. [commitizen](https://commitizen-tools.github.io/commitizen/): Create better commits

## What is pre-commit?

Pre-commit is a python package which acts as a framework for managing pre-commit hooks. Pre-commit allows you to run several staged code checks prior to committing your changes.

## Why use pre-commit?

To save time and money! Once a code gets pushed into git and your pipeline kicks off, it can take a few seconds or minutes to find where an error came from. So using pre-commit we can prevent simple errors such as terraform validation or even simpler YAML lint!

## Where do I start with pre-commit?

We are going to add a pre-commit config to the terraform code we have built in the previous post. If you haven't gone through it or don't want to, that is absolutely fine. Add a pre-commit config to any code you are working on and make it a habit. Adding a pre-commit config to a template repository is a perfect way to introduce this tool to a wider team.

### Prerequisites

- [ ] I have python installed. [Installing Python](https://www.python.org/downloads/)
- [ ] I have an IDE installed. An IDE such as VSCode of PyCharm is recommended.
- [ ] I have configured Git [Instructions can be found here](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)
- [ ] I have some code ready.
- [ ] I have pip installed. [Installing pip](https://pip.pypa.io/en/stable/installation/)

#### Step 1

* Install pre-commit

```bash
pip install pre-commit
```

#### Step 2

* Check that pre-commit is installed

```bash
pre-commit --version

## output should look similar to this

pre-commit 2.17.0

```

#### Step 3

* Create a config file for pre-commit

```bash
touch .pre-commit-config.yaml
```

* The config file will tell pre-commit what tests we need it to run.

#### Step 4

* Populate the config file with the following

* List of hooks available [here.](https://pre-commit.com/hooks.html)

```YAML
---
-   repo: https://github.com/pre-commit/pre-commit-hooks #name of repo with hook
    rev: v4.1.0 # version of the repo (tag) or branch
    hooks:
    -   id: trailing-whitespace #id of the hook. Some repos have multiple hooks
```

#### Step 5

* Stage your code and run pre-commit

```bash

git status
git add .
pre-commit install # run pre-commit every time you run a commit command. You can skip it for now.
pre-commit run --all-files

#output should look something like this
Trim Trailing Whitespace.................................................Passed
```

* If you have followed the terraform related blog then add the following hooks and run pre-commit

```YAML
-   repo: https://github.com/gruntwork-io/pre-commit
    rev: v0.1.17
    hooks:
      - id: terraform-fmt
      - id: terraform-validate
      - id: tflint
```

#### Step 6

* This is an optional step, where we will look at what markdown rules we would like **not** to enforce. The reason behind ignoring a test is when you require a certain line to be beyond a given length in case of yamllint or if you want your markdown to look a certain way.

The code snipette below will tell the markdownlint test to accept a configuration that has been set within the `.ci/.mdlrc` path.

```YAML
-   repo: https://github.com/jumanjihouse/pre-commit-hooks
    rev: master
    hooks:
    -   id: markdownlint
        args: ['-c','.ci/.mdlrc']
```

#### What does the config file look like?

* Let's have a look at the folder structure required prior to defining the test exceptions.

```bash
|- .ci                      #<--- This is a new directory
|   |- .mdlrc               #<--- This is a new file
|   |- markdown.rb          #<--- This is a new file
|- main.tf                  # These
|- providers.tf             # files
|- repositories.yml         # already exist.
|- README.md                # This example structure builds
|- .pre-commit-config.yaml  # on the terraform 101
|- .gitignore               # blog post

```

* It is not a must for the directory to be called `.ci`. Since `args: ['-c','.ci/.mdlrc']` specifies a path the directory can be called anything but recommend `.ci`. The directory has two files.

1.

* The `.mdlrc` file with the following content.

* This file simply points to another file with the style definitions.

```markdown
style '.ci/markdown.rb'
```

2. The `markdown.rb` file with the actual rule exceptions defined. This is the file that you can populate with the exceptions you require for the code base.

The syntax is the foll0wing

```ruby
all
exclude_rule '<MDrule number>'

```

For example the configuration below enforces `all` Markdown rules excluding `MD002`.

```ruby
all
exclude_rule 'MD002'

```

* Please note that this is the rule exception strategy for markdownlint. Other pre-commit hooks will have a different syntax, which should be documented within the hook's repository.

* Great now we have good looking code. Let's commit!

## What is commitizen?

Commitizen is a Python package for writing descriptive and standardized commits across teams. It also allows for changelog creation once you release a new version of the code base you are working on.

## Why use commitizen?

1. Create a commit standard across your team.
2. Capture work items (Jira tickets or Azure board tickets) within your commit.
3. Create commit prefixes that you can trigger pipelines base on.
4. Many more!

## Where do I start with commitizen?

* You just need to install this tool and create great commits

#### Step 1

```bash

pip install -U commitizen

#alternatives

sudo pip3 install -U Commitizen

brew install commitizen

```

#### Step 2

* Commit!

```bash
cz commit
```

## Final remarks

* Bring it all together

```bash
git status
git diff ## only if you know how to get out of vim
git add .
pre-commit run --all-files
git add . ## pick up changes made by pre-commit
cz commit
git push
```

* Well done! I hope you have enjoyed this short post on better git commits.
