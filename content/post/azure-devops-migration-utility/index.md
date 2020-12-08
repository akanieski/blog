+++
author = "Andrew Kanieski"
title = "Moving Work Items Made Easy"
date = "2020-12-08"
description = "Managing 'Cleanup' Jobs Being Processed by Your Agents with Azure DevOps"
tags = [
    "azure devops",
    "devops"
]
+++
In my experiences working with enterprise implementations of **Azure DevOps Server and Services** I've found it fairly common for organizations to be able to migrate work items and other entities. Some common scenarios include:

- Moving work items from XML Process Model to Inheritance Process Model
- Moving work items from on-premise Azure DevOps Server to Azure DevOps Services
- Moving work items from between process models that have different fields and requirements
- Moving work items from many projects into a single project
- Moving work items between projects for other reasons

Sometimes these scenarios may be simple, just move item 1 from project A to project B. But in other scenarios they can be fairly complex. For example, moving a work item from a **CMMI XML Process Model** to an **Agile flavored Inheritance Process model**. This would require field mappings from one field to another including work item type changes. As these sorts of scenarios come up it can be daunting creating a set of scripts to automate this process using the out-of-the-box REST api for Azure DevOps.

That being said I would like to introduce a project I've been working on the ... 

### [Azure DevOps Migration Utility](https://github.com/akanieski/azure-devops-migrator). 

This tool was written to create a simple user experience for migrating work items and other entities between projects in Azure DevOps. Working directly with the Azure DevOps REST api takes work. Administrators may not have the time needed to script out migrations using the REST API. This tool aims to make that experience easier.

This tool is maintained by it's author Andrew Kanieski with the support of the community and is not an official or supported product of Microsoft. It's design and intent does not relect the views of Microsoft.

A special thanks to the teams of people that I have worked with over the years that have lent ideas and requested features that have found their way into this tool. Together as a community we can make our work easier and more enjoyable!

![UI Screenshot](migrator_running.png)

##### Download the latest version [here](https://github.com/akanieski/azure-devops-migrator/releases).

If you are looking for a feature that's missing or need to report a bug, please follow up in our [GitHub's Issues section](https://github.com/akanieski/azure-devops-migrator/issues/new/choose).

*Disclaimer: This product is not a Microsoft product and support for it is handled by the community. Please keep this in mind when using it. If you encounter an issue feel free to submit a bug using the link above*