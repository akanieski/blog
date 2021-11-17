+++
author = "Andrew Kanieski"
title = "Mapping Azure Repo Links After Work Item Migration"
date = "2021-11-16"
description = "Recently I worked on a project to 'fix-up' Azure Repo links on work items that were imported from another instance of Azure DevOps"
tags = [
    "azure devops",
	"azure devops services",
	"devops",
    "vsts"
]
+++

There are a number of open source tools for migrating work items from one Azure DevOps Organization to another and even from Azure DevOps Server to Azure DevOps Services. But what happens when you migrate a work item from the source that happens to reference a Branch, Pull Request or Commit that exists on an Azure Repo that used to reside in the source, but now lives inside the target? 

If the open source tool your using does not support mapping between the Git Repo Links you may find yourself with work items that have broken Git Links. 

They will look something like this when they fail to map to a proper linked Git Repo:

![UI Screenshot](/media/bad-git-links.png)

Fortunately, the [Azure DevOps SDK](https://docs.microsoft.com/en-us/azure/devops/integrate/concepts/dotnet-client-libraries?view=azure-devops) is fairly robust and easy to work with. Couple this with a quick development environmenet courtesy of [GitHub CodeSpaces](https://github.com/features/codespaces) and we have an easy solution to this problem: a simple command line tool that maps git links on the target work items that reference repos in the source by repository name.

See the code below, or download the latest executable from the [GitHub Releases Page](https://github.com/akanieski/git-links-mapper/releases)

{{< rawhtml >}}
<style>
code.cs.hljs {
    height: 500px;
}
</style>
<script src="https://emgithub.com/embed.js?target=https%3A%2F%2Fgithub.com%2Fakanieski%2Fgit-links-mapper%2Fblob%2Fmain%2FProgram.cs&style=github&showBorder=on&showLineNumbers=on&showFileMeta=on&showCopy=on"></script>
{{< /rawhtml >}}
 

__Lookout for upcoming articles on [GitHub CodeSpaces](https://github.com/features/codespaces) and some tools around tracking down [Trojan Source Attacks](https://trojansource.codes/)__!

Happy Coding!

**-- Andrew Kanieski**
