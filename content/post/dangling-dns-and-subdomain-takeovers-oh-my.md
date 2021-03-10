+++
author = "Andrew Kanieski"
title = "Dangling DNS and Subdomain Takeovers - Oh My!"
date = "2021-03-09"
description = "Dangling DNS and Subdomain Takeovers - Oh My!"
tags = [
    "frontdoor",
    "dns",
    "azure",
    "security"
]
+++

It's a busy work week, your backlog seems neverending, you're rushing to get things pushed out to production. 
You think I've got a new configuration for my Frontdoor that I want to deploy, I'll just tear down the old one 
and push that ARM template to deploy it's replacement. You fire off the delete command. Once it's done you push
the latest scripts for deployment and go get coffee. You comeback to find that although the delete was successful
the deployment failed. You check the error logs, "Name already in use".

You think, meh, no problem, I'll just run the deployment, maybe the delete hadn't fully committed before the 
replacement was deployed with the same name. You run it again, "Name already in use". You triple check. Same. You
go to your resource explorer looking for the Frontdoor with the same name. It's not there. What's going on?? 

You go to visit your application to see if it's running, you swing over to `app.sample.com` which should, by way 
of a CNAME entry on your domain, route you directly to your Frontdoor. You find that the website takes you to some other
website. Another website, being hosted under **your** subdomain. Have I been hacked?

The scenario I describe above is what's known as a "Dangling DNS Subdomain Takeover", and is a common way for bad 
actors to gain unintended access to hosting a site in **your** subdomain. Let's break down how it works!

First off, what is a CNAME entry? A CNAME is a DNS record that allows it's owner to point the DNS resolution for 
a given domain or subdomain to another hostname. It is a commonly used tool to direct traffic to a multi-tenanted  
or shared hosting environment, like Azure Frontdoor, Azure App Services, Google's App Engine or Amazon's Elastic 
Beanstalk. So in practice the CNAME entry for our example above might look like this. 

```
CNAME  app.sample.com  ->  app-sample-demo.azurefd.net
```

In this example we have a subdomain, `app.sample.com` pointing to `app-sample-demo.azurefd.net`. This allows us to 
host our site behind Azure Frontdoor using the user defined url from Azure Frontdoor. Now let's say your using a
custom domain and custom SSL certificate on `app.sample.com`, so your not using Azure DNS to manage your DNS, for 
reasons I'll get into later in this article.

So put yourself into the grimy shoes of a malicious actor, trying to steal cookies from the app hosted in your 
subdomain. After some reflection you might realize there is a weakness in this chain. What happens if the app owner 
deletes the shared resource that, even if just for a moment? Many hosting providers will release the auto generated 
hostname assigned to the resource. In the case of my example, the app-sample-demo.azurefd.net get's released. You 
think to yourself, what if I write a script to swoop in as soon as it's deleted, and provision *my own* Azure 
Frontdoor instance with the same name as the target of our attack, which is now newly released and ready to be 
reused.

You can see where this is going. Now a brand new Azure Frontdoor is online, with the same name as your old Frontdoor 
except it's not owned and operated by you! They spin up their malicious app, and maybe they collect your user's cookies 
or worse they create an identical login screen and start harvesting user's credentials. 

This is essentially a typical "Dangling DNS Subdomain Takeover". The "Dangling DNS" refers to the CNAME entry that is 
left pointing to a non-provisioned resource in a multi-tenanted hosting service. 

### How to mitigate?
In the scenario described above there are a handful or mechanisms customers can employ to mitigate this risk. Microsoft 
has a fantastic article that explains this type of attack, and lays out a few key ways of mitigating and some ways of 
stopping it dead in it's tracks before it even happens. Read more [here](https://docs.microsoft.com/en-us/azure/security/fundamentals/subdomain-takeover#use-azure-dns-alias-records). Use of Azure DNS with "Aliases" addresses this issue. Also note in the article there are other proactive 
measures that can be taken even if your not ready to Azure DNS.

This particular issue is not one that is unique Azure, and the solutions are also not unique to Azure. Securing your 
domains and DNS entries from this sort of attack is crucial to maintaining security! 

**The key takeaway, never delete a resource that backs a CNAME entry in your DNS without first redirecting or removing 
the CNAME record first!**
