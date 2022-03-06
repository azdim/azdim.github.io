---
title: "Terraform 101"
description: "An introduction to terraform"
date: 2022-02-27
images: ["avatar2.jpg"]
tags: ["terraform"]
---
## What is terraform?

Terraform is a widely used Infrastructure as Code (IaC) tool. This is a tool that is cloud agnostic, therefore can interact with a wide range of supported cloud providers.

## Why use terraform?

As an engineer you may find yourself having to provision several resources on a cloud platform. If you were to create it manually through the CLI or a webportal you would find that creating copies of the same resource can be time consuming. Furthermore removing these resources can prove to be tedious as one resource may also have other attached resources that also need to be removed. As an example if you were to provision a virtual machine on any cloud provider you would need to have a boot volume attached to it which is created at the time the virtual machine is created. If you were to delete the virtual machine it is possible that the volume is still lingering around which might also affect your overall cost. Terraform overcomes this problem by using something called a state file ```terraform.tfstate```. This file contains all the information of the resources that were provisioned and therefore if you were to delete your resources terraform would make sure to delete all that was created.

## Where do I start with terraform ?

Let's begin with a very simple yet effective project that will cover many aspects of terraform and should give you the foundation you need.

### Project goal

The goal of this project is to create a repository on GitHub using terraform. We will manually create a repository which will contain the initial terraform code, thereafter all future code repositories will be created and managed using terraform.

### Prerequisites

- [ ] I have an IDE installed. An IDE such as VSCode of PyCharm is recommended.
- [ ] I have terraform installed. Terraform can be installed through brew (Mac), Chocolatey (Windows) and apt for Linux [Instructions](https://www.terraform.io/downloads)
- [ ] I have configured Git [Instructions can be found here](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)
- [ ] I have a GitHub account.
- [ ] I have added my ssh key to GitHub [Instructions can be found here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)
- [ ] I have a GitHub token saved. This token should have repo level access. [How to create a PAT token.](https://docs.github.com/en/enterprise-server@3.3/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
- [ ] I have created an empty repository manually. You can call this repository terrafrom_github_repository (a naming convention always helps).

#### Step 1

Clone the created github repository and checkout to a new branch

```bash
git clone git@github.com:<username>/<repo>

git checkout -b initial_terraform_code
```

#### Step 2

* Create the following files

```bash
# you can use the following command in bash or make the files manually

touch main.tf providers.tf .gitignore repositories.yml README.md

```

#### Why did we create these files?

---

|File name|Purpose|
|:--|--:|
|main.tf| This file will contain some terraform configuration|
|providers.tf|This file will also contain some terraform configuration|
|repositories.yml| This file will contain repository related configuration|
|README.md| A readme file is always recommended. Explain what this repo is.|
---

#### Does terraform care how many or how few .tf files are in on repo?

* Terraform does not care but you should. Think of .tf files as parts of a recipe. Terraform will merge all these parts together and create the resources. However in terms of best practices it is compulsory that we create .tf files that each contain a purpose. When your code works it is great, but its about preparing for when it doesn't. Fracturing the recipe allows you the developer to resolve issues much faster. ```main.tf``` is a file that is commonly found in a terraform code repository and usually contains the core infrastructure that is created. ```providers.tf``` or ```versions.tf``` is where terraform providers are mentioned. Providers are what allows terraform to create infrastructure inline with a given cloud provider. Microsoft calls their virtual machine virtual machines but Amazon calls them EC2 instances. A provider allows you to configure resources according to the platform upon which they will be provisioned. More on providers later.

#### How do I populate these files?

* There are two web site that are crucial to any terraform related development regardless of skill level.

1. [Terraform registry](https://registry.terraform.io)

2. [Terraform syntax](https://www.terraform.io/language)

* Terraform registry hosts all the necessary configuration that you will need to deploy infrastructure to any cloud provider.
* Terraform.io will provides you with the syntax of HCL2 (Hashicorp Configuration Language).

#### step 3

* Navigate to terraform registry, select browse modules and enter the following into the search bar ```operatehappy/repository```.

* ```operatehappy/repository``` is an example of a **Terraform Module**. The concept of a [terraform module](.https://www.terraform.io/language/modules/develop) is an extremely important terraform concept and one that requires its own blog post. So for now just remember that a terraform module is a single interface that allows you to create repeatable infrastructure.

* If you had a chance to glance over the Github repository for this module you would have come across the required [dependencies]. The dependency for this module is the [GitHub provider](https://registry.terraform.io/providers/integrations/github/latest/docs). Go ahead and click on use provider on the top right hand corner. Copy the code block into as you guessed it `providers.tf.`

```terraform
terraform {
  required_providers {
    github = {
      source = "integrations/github"
      version = "4.20.1"
    }
  }
}

provider "github" {
  
}
```

#### Step 4

* Copy and paste the following code block onto your `main.tf`.

```terraform
locals {
  terraform_prefix = "terraform"
  repo             = yamldecode(file("repositories.yml"))
  topics           = ["github"]
}



module "myrepos_module" {
  for_each               = local.repo.repos.repositories
  source                 = "operatehappy/repository/github"
  version                = "3.0.0"
  name                   = each.value.name
  visibility             = each.value.visibility
  description            = each.value.description
  topics                 = compact(flatten([local.topics, try(each.value.topics, "")]))
  license_template       = "apache-2.0"
  allow_merge_commit     = false
  delete_branch_on_merge = true
  default_branch         = "main"
}
```

#### Breaking down the code

<table>
<tr>
<td> Code Block </td> <td> Breakdown </td>
</tr>
<tr>
<td> 

```terraform
locals {
  terraform_prefix = "terraform"
  decoded_repos            = yamldecode(file("repositories.yml"))
  topics           = ["github"]
}

```
<td>

**locals**  stanza can (stanza in HCL2 is a code block that capture information with {} brackets) be thought of as a list on internal variables. Here we have defined a variable terraform_prefix as string terraform, topics as an array which has a single string element github. And then we have the repo variable which is a dictionary and makes use of a built-in terraform function.

</td>

</td>
</tr>
<tr>
<td> 

```
module "myrepos_module" {
  for_each               = local.decoded_repos.terraform_repos.repositories
  source                 = "operatehappy/repository/github"
  version                = "3.0.0"
  name                   = each.value.name
  visibility             = each.value.visibility
  description            = each.value.description
  topics                 = compact(flatten([local.topics, try(each.value.topics, "")]))
  license_template       = "apache-2.0"
  allow_merge_commit     = false
  delete_branch_on_merge = true
  default_branch         = "main"
}
```

</td>

<td>

The GitHub module interface  as I like to call it (formally called the module block) which takes in user inputs and creates repositories based on those inputs. There are several key functions that at first glance may not seem apparent. I will elaborate further on these built-in terraform features after populating the repositories.yml file.

</td>
</tr>
</table>

* There are several key functions that at first glance may not seem apparent. I will elaborate further on these built-in terraform features after populating the repositories.yml file.

#### Step 5

* Populate the repositories.yml file with the following. I recommend you change the values and test it on your own.

```YAML
---
terraform_repos:
  description: "This is a terraform built repository."
  repositories:
    terraform_repo:
      name: "terraform_repository_1"
      description: "This is my first terraform repository."
      topics: ["terraform","myfirstrepo"]
      visibility: "public"

```

* If you look at the locals within main.tf you will find that the variable `repo` is equal to `yamldecode(file("repositories.yml"))`. There are two built-in features here the first is [`file`](https://www.terraform.io/language/functions/file), which is a file system function that reads the contents of a file you can think of file as being similar to the `cat` bash function. The second function is [`yamldecode`](https://www.terraform.io/language/functions/yamldecode) which decodes YAML strings and produces key value pairs. So when you put both together the `file` function reads the repositories.yml file and provides the input for the `yamldecode` function, which will produce key-value pairs.

* Now within the module block an argument [`for_each`](https://www.terraform.io/language/meta-arguments/for_each) has been defined. Imagine the you have a function f(x) = x + 1 and you want to iterate f(x) through a range of x value you would use a for loop in Python. The `for_each` argument is very similar but in this case f(x) is the infrastructure created through the module and x is whatever `for_each` has been defined as.

* The `for_each` argument here is set to `local.decoded_repos.terraform_repositories.repositories`. In other words you are asking terraform the following, for each repository value defined under repositories (line 4 YAML file) under the terraform_repositories group (line 1 YAML file) within the repositories.yml file create an instance of this module. That definition can be confusing but creating multiple repositories might help you understand it better. However for this project we will start with one.

---

## Look back at the module block

<table>
<tr>
<td> Code Block </td> <td> Purpose </td>
</tr>
<tr>
<td> 

```terraform
for_each               = local.repo.repos.repositories
```

</td>

<td>

How many repositories need to be created but also create a key-value pair. The key is the value at line-6 of the YAML file. And all the attributes within that key become values of that key.

</td>

</tr>

<tr>
<td> 

```terraform
source                 = "operatehappy/repository/github"
```

</td>

<td>

Where should terraform get this repository from. Remember since this module is available within terraform registry all you need to set as a source is the name of the module. If however you need to use your own module hosted in git you can refer to [this link](https://www.terraform.io/language/modules/sources).

</td>

</tr>

</tr>

<tr>
<td> 

```terraform
version                = "3.0.0"
```

</td>

<td>

Version of the module

</td>

</tr>

<tr>

<td> 

```terraform
name                   = each.value.name
```

</td>

<td>

As mentioned within the for_each explanation. The keys created by the for_each argument will contain values associated with those keys. These values can the be referred using each.value.some_value_name and in this case some_value_name is **name** (line-6 YAML). Therefore each.value.name will be **terraform_repository_1**

</td>

</tr>

</tr>

<tr>

<td> 

```terraform
visibility             = each.value.visibility
description            = each.value.description
```

</td>

<td>

These follow the same explanation as name.

</td>

</tr>

</tr>

</tr>

<tr>

<td> 

```terraform
topics                 = compact(flatten([local.topics, try(each.value.topics, "")]))
```

</td>

<td>

More built-in tricks! I will explain these built-in features right after breaking down the module. But topics will set the topics or labels for this repository, which can be seen in the About section of the repository. In this example the topics are terraform, github and myfirstrepo.

</td>

</tr>

<tr>

<td> 

```terraform
license_template       = "apache-2.0"
allow_merge_commit     = false
delete_branch_on_merge = true
default_branch         = "main"
```

</td>

<td>

The license file is available on GitHub by default. **allow_merge_commit** is disabled so not all commits are added to the main branch. **delete_branch_on_merge** this will delete the remote branch once a pull request is complete. **default_branch** is set to main.

</td>

</tr>

</table>

### Breakdown of the topics variable

```terraform
topics                 = compact(flatten([local.topics, try(each.value.topics, "")]))
```

* Let's begin with try. The [try](https://www.terraform.io/language/functions/try) function will return the value that is first to not give an error. So in this case `try(each.value.topics, "")` so if the topics value is not defined within the YAML file instead of giving an error due to a null value the try function will output and empty string `""`.

* The result of the try function is then passed onto flatten. [Flatten](https://www.terraform.io/language/functions/flatten) will merge any lists within a list into one long list. In this case we have defined `local.topics` which is equal to `["github"]` and `each.value.topics` which is equal to `["terraform","myfirstrepo"]`. Flatten will create a new list `["terraform","myfirstrepo","github"]`.

* This is then passed onto compact. [Compact](https://www.terraform.io/language/functions/compact) removed any empty strings from a list. So if `each.value.topics` had no value the `try` function would output `""` which would then be flattened into `["github", ""]` and flatten would then remove `""` which would result in the final list being `["github"]`.

* I know I didn't need to complicate `topics` this much, however it was a good opportunity to show built-in functions that may prove to be useful to you later.

#### Step 6

* Export your GitHub PAT token as an environment variable. This was a prerequisite.

* Run the following command to initialise terraform. This will download a local copy of the module and any other dependencies. It will also create a local terraform state file which will hold the configuration of the infrastructure deployed through terraform. A `.terraform.lock.hcl` file will be created and will hold version of to provider. As a best practice the `.terraform, .terraform.tfstate , .terraform.lock.hcl and terraform.tfstate.backup` files should all be in your `.gitignore`. In a production environment you will be using a remote backend. However that costs money and we will not need that for this project.

```bash

export GITHUB_TOKEN=123234254534

terraform init

```

#### Step 7

Let's plan what infrastructure terraform will be creating. Notice I am using the `-out.tfplan` flag here. This is not necessary but I recommend you start getting in the habit of doing it to avoid any terraform related confusion. This command will create a `plan.tfplan` file which should also be within the `.gitignore`

```bash

terraform plan -out=plan.tfplan
```

#### Step 8

* Let's create the repository!


```bash

terraform apply plan.tfplan

```

#### Step 9

* Go checkout your repo! And add to the repositories.yml and plan and apply the changes and see what happens.

## Final remarks

* Thank you for making it this far I understand that I covered some high level concepts in 101 blog. I hope you will find this useful either personally or within an organization you are a part of. You have not only learned the basics of terraform but also used some clever built-in tools. I did not cover terraform variables or outputs but I will in a future post. Thank you!