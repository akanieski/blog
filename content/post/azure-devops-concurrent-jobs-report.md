+++
author = "Andrew Kanieski"
title = "Measuring Concurrent Jobs Over Time"
date = "2020-07-17"
description = "Learn how to get a feel for how many concurrent jobs you've ran at most in the past year."
tags = [
    "azure-devops",
    "azure",
    "devops",
    "concurrent-jobs",
    "parallel-jobs"
]
+++
If you are looking to move your Azure DevOps Server or TFS implementation to Azure DevOps Services, you will find that the licensing and pricing around concurrent job's has changed.

First, what is a **"concurrent job"** anyway? In short, a job can mean a few different things in Azure DevOps, but a concurrent job relates to a discrete unit of work executed as part of a pipeline (Release or Build). That being said you may find it somewhat difficul to gauge how many concurrent pipelines you are currently using **at most** during the course of business. 

To get a grasp of how many concurrent jobs you have consumed **at most** during the past year, the below query will give you a decent idea of consumption:


{{< gist akanieski 78016db83dcf2b3ea1651dd09af0f5af >}}

Enjoy!
