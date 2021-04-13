+++
author = "Andrew Kanieski"
title = "GitHub Gists Secrets Scanner"
date = "2021-04-13"
description = "Use powershell to scan your staff's public GitHub accounts for GitHub and Azure DevOps Personal Access Tokens"
tags = [
  "secrets",
	"tokens",
  "pat",
  "pats",
  "github",
  "gist",
  "gists",
  "azure",
  "azure devops",
  "devops"
]
+++

Do you manage a team of developers that love to share code with eachother and the community? Chances are your team is likely 
using Github's Gist feature to share quite snippets of code with eachother and even the community. This feature of GitHub is 
extremely useful as a light weight code sharing and reference tool! But along with it's ease of use comes the all-to-common 
mistake of pasting and saving your tokens and secrets that are not safe to be shared publicly.

The below powershell script can be used to identify Azure DevOps Personal Access Tokens and GitHub Personal Access Tokens in 
your team's public GitHub accounts.

Let's get started!

## Getting Started
First let's go ahead and setup a CSV file that contains 3 columns, only one column is really needed, the `GithubUsername` 
column. The other columns are used for make the output report a little more denormalized.

Your input CSV should look something like this:

```
"GitHubUsername","FullName","Email"
"akanieski", "Andrew Kanieski", "sample@andrewkanieski.com"
```

You can populate this list with the known GitHub usernames of all your team members.

## Scan Gists
At this point you can go ahead and run the tool and it will scan each team members public facing Gists for GitHub Personal 
Access Tokens.

The next step is it will cross reference each PAT and validate whether it is currently in use or not by attempting to use it 
to access the GitHub REST API. If it is successfull it will mark it as a "confirmed" token.

## Results!
Below you will see the breakdown of suspicious secrets in your team's GitHub accounts as well as whether or not the PAT is 
active. You will also get a CSV report with the details

![Alt text](/ReportOutput.png "Report Summary")

## What about Azure DevOps?
Just like above, the script can scan our team members public Gists, but instead of verifying if the token is valid and active 
with GitHub, it can confirm of it is active with Azure DevOps, both "Services" and "Server". 

## Identifying Secrets and Tokens
Currently the script uses a simple regular expression to identify the personal access tokens. More advanced secrets scanner's 
, like Microsoft's CredScan and the scanning features of GitHub Advanced Security, use other algorithms to identify possible 
secrets in your code. A common way of identifying a secret is to measure a strings entropy. But alas, the goal of this script 
is to provide a quick way of scanning, not to build a robust scanning tool. 

## GitHub REST Throttling
As you use this script you will quickly find that GitHub throttle's it's API access quite aggressively, with a rolling per-hour 
request rate of 60 requests per hour. If you pass a `GithubUsername` and `GithubToken` to the script, it will use these to 
authenticate with GitHub, increasing your per-hour request-rate to 5000 requests per hour.

Enjoy!


{{< gist akanieski d1944c12070f274792327738d5e2620b >}}
