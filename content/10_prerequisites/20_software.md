+++
title = "Installs & Configs"
chapter = false
weight = 20
+++

Before we begin coding, there are a few things we need to install, update, and configure in the Cloud9 environment.

### Installing and updating

In the Cloud9 terminal, **run the following commands** to install and update some software we'll be using for this workshop:

First update the AWS CLI
```
pip install --user --upgrade awscli
```

Next Install and use Node.js v8.10
```
nvm install v8.10.0
nvm alias default v8.10.0
```

{{% notice note %}}
These commands will take a few minutes to finish.
{{% /notice %}}

### Configuring a default region 

A best practice is to deploy your infrastructure close to your customers, let's configure a default AWS region for this workshop : Northern Virginia (*us-east-1*) for North America or Ireland (*eu-west-1*) for Europe and Africa, (*eu-west-1*).

**Create an AWS config file**, run:

{{% tabs %}}
{{% tab "us-east-1" "North America" %}}
```bash
cat <<END > ~/.aws/config
[default]
region=us-east-1
END
```
{{% /tab %}}

{{% tab  "eu-west-1"  "Europe and Africa" %}}
```bash
cat <<END > ~/.aws/config
[default]
region=eu-west-1
END
```
{{% /tab %}}
{{% /tabs %}}
