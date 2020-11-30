+++
author = "Andrew Kanieski"
title = "Take Inventory of SSH Keys Used Across Your Userbase"
date = "2020-02-14"
description = "Learn to take inventory of all SSH Keys used by your Azure DevOps Users"
tags = [
    "Azure DevOps",
	"Azure DevOps Server",
	"DevOps",
	"devops",
	"ssh"
]
+++

Have you ever needed to take an inventory of who is using SSH Keys across your organization? 
You will quickly find that there is no conventient place in the Azure DevOps Server user interface 
to quickly report on how many keys are out there and when they expire. This could be useful if you 
are performing an audit of SSH Keys or looking to determine how many users are using SSH to access 
their Git repositories.

The below query will help you determine this information quickly:

{{< gist akanieski eecf19f1e05af7c6c732898cbf51da5a >}}


**Note: This query itself is not supported. It will work and has been tested with Azure DevOps Server 2019 and likely will continue to work. This of course can change with subsequent versions. Your mileage may vary.**