---
title: "Nomad 101"
description: "An introduction to nomad"
date: 2022-02-27
images: ["avatar2.jpg"]
tags: ["nomad","Hashicorp"]
---

## What is Nomad?

Nomad is an application/workload scheduler and if often compared to Kubernetes. Kubernetes and Nomad share similarities but are also different they can be used in conjuction to one another as well as individually. Depending on the requirement the tool will change. If you have Docker workloads that need to scale out with zero downtime then Nomad is the perfect tool. As of writing this article Nomad is at version 1.2.6 and has many issues that are being addressed especially with stateful workloads.

## Why use Nomad?

Most organistations or individuals use dockerised services. These services need to run with zero downtime and also scale out to meet demands. While Kubernetes can be used to achieve this, it has become an increasingly complex tool to configure and use. And therefore for an organisation looking to deploy an effective workload scheduler/orchestrator with reduced complexity then Nomad is the perfect tool to use. Nomad also supports various plugins such as CSI for stateful workloads.


## Where do I start with Nomad?

The easiest way to get started with Nomad is to use it in what is called developer mode. This will allow you to experiment with it and run several applications.

* Installing Nomad:
