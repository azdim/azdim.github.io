---
title: "Packer 101"
description: "An introduction to packer"
date: 2022-02-27
images: ["avatar2.jpg"]
tags: ["packer","Hashicorp"]
---

## What is packer?

Packer is a compute image build tool which would allow a user to build custom compute images.

## Why use packer?

The reason this is required is because the out of the box images such as Ubuntu only has the basic packages installed. What if you wanted your machine to run as webserver on boot or a Nomad Client (Nomad will be introduced in a future post)? In order to run packages with custom configuration at boot a Custom compute image is required and not all systems will be running the same OS some will require other OS systems such as CentOS or Windows.

## Where do I start with Packer?

* First we need to install packer.
