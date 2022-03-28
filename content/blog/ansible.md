---
title: "Ansible 101"
description: "An introduction to Ansible"
date: 2022-02-27
images: ["avatar2.jpg"]
tags: ["ansible"]
---

## What is Ansible?

Ansible is an open source configuration management tool offered by RedHat.

## WHy use Ansible?

* If you are working with any kind of remote compute at scale whether the platform is hosted within the Cloud or not Ansible is a core tool that you will need to familiarise yourself with. The open source tool only works with Linux based systems which makes up most of any remote platform. For Windows you would used Ansible tower which requires a license.

* Imagine that you need to install Terraform onto your machine. You would need to run a couple of CLI commands and you would have it installed. Now imagine that you need to install Terraform onto 100 machines, now the task isn't so easy. Connecting to those machines through SSH and running the CLI commands will likely consume a large amount of valuble time and that is where Ansible comes in. By defining what is known as a playbook you are able to run the same configuration onto several machines cutting down the time taken and also eliminating human error.

* Using Ansible also allows you to configure your platform within a pipeline.

## Where do I start with Ansible?

* Install Ansible:

```python -m pip install ansible-glaxy```

* Create a file called `playbook.yml`. The name of the file is not a hard dependency but keeping with best practices the file should be called as `playbook.yml` or `main.yml` in a situation where you have mutliple playbooks.

```bash
touch playbook.yml
```

* Create a file called `hosts`. Like the playbook file this file can be called anything but we will stick to `hosts` or `ansible-inventory` inline with best practices.

```bash
touch hosts
```

* The playbook.yml file will contain the configuration we would like to run and hosts will contain the IPs of the machines that playbook will run on.

## Project Goal

* We will begin with a simple ping-pong playbook. The playbook will send a ping to an IP address if the package was received then the machine with the IP address will send out message or a pong.
