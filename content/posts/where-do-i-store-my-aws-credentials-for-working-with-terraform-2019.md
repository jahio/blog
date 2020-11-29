+++
author = "J. Austin Hughey"
categories = ["aws", "devops", "terraform"]
date = 2020-06-11T08:18:34Z
description = ""
draft = false
slug = "where-do-i-store-my-aws-credentials-for-working-with-terraform-2019"
tags = ["aws", "devops", "terraform"]
title = "Where do I store my AWS credentials for working with Terraform? (2019)"

+++


It's always a good practice to keep your authentication/authorization details out of your code (or anything that'll be shared with others via source control, etc.). So when working with AWS and Terraform, how do you _securely_ tell it what your AWS credentials are?

The answer: `$HOME/.aws/credentials`

(On Windows, `"%USERPROFILE%\.aws\credentials"`.)

This is going to be a plain text file named "credentials" in that directory. A minimal implementation will contain just 3 lines. Here's an example:

```
[default]
aws_access_key_id=REDACTED
aws_secret_access_key=REDACTED

```

> Where do I get those two keys?

Once logged into the AWS Console, click your username at the top, then, "My Security Credentials", then "Create New Access Key". **You'll only see this ONCE when you create it, so copy/paste that keypair somewhere safe.**

{{< figure src="https://github.com/jahio/jahio.github.io/raw/master/assets/aws-access-keys.png" >}}

...somewhere like `$HOME/.aws/credentials`.

> How do I tell Terraform to use this file?

You don't have to - if it's named and placed as above, it'll pick up those credentials **automatically**.

