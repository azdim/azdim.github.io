---
title: "Terraform 101"
description: ""
date: 2022-02-27
images: ["avatar2.jpg"]
tags: ["about"]
---

## What is terraform?

Terraform is a widely used Infrastructure as Code (IaC) tool. This is a tool that is cloud agnostic, therefore can interact with a wide range of supported cloud providers.

## Why use terraform?

As an engineer you may find yourself having to provision several resources on a cloud platform. If you were to create it manually through the CLI or a webportal you would find that creating copies of the same resource can be time consuming. Furthermore removing these resources can prove to be tedious as one resource may also have other attached resources that also need to be removed. As an example if you were to provision a virtual machine on any cloud provider you would need to have a boot volume attached to it which is created at the time the virtual machine is created. If you were to delete the virtual machine it is possible that the volume is still lingering around which might also affect your overall cost. Terraform overcomes this problem by using something called a state file ```terraform.tfstate```. This file contains all the information of the resources that were provisioned and therefore if you were to delete your resources terraform would make sure to delete all that was created.

## Where do I start with terraform?

Let's begin with a very simple yet effective project that will cover many aspects of terraform and should give you the foundation you need.

### Prerequisites

- [ ] I have an IDE installed. An IDE such as VSCode of PyCharm is recommended.
- [ ] I have terraform installed. Terraform can be installed through brew (Mac), Chocolatey (Windows) and apt for Linux [Instructions](https://www.terraform.io/downloads)
- [ ] I have a GitHub account.
- [ ] I have a GitHub token saved. This token should have repo level access. [How to create a PAT token.](https://docs.github.com/en/enterprise-server@3.3/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
