+++
author = "Andrew Kanieski"
title = "Tracking Adoption of DevSecOps using Azure DevOps Server"
date = "2020-07-17"
description = "A quick solution to tracking the adoption of DevSecOps tools like Artifactory, JFrog and SonarQube across your Azure DevOps footprint"
tags = [
    "azure-devops",
    "azure",
    "devops",
    "tfs",
    "artifactory",
    "jfrog",
    "sonarqube",
    "security",
    "devsecops"
]
+++
Recently I sat down with a customer looking to understand how dilligent have their application teams been with adopting the industry standard tools for DevSecOps. 

This particular customer is an avid consumer of the popular static analysis tool SonarQube as well as the dependency and artifact management tool Artifactory.

Although the tools had been present within their enterprise for sometime, they needed to get a grasp of have often these tools were being used in relation to the ever growing number of repositories sprouting up in the field.

Please find below a quick SQL query for getting a summary of Build Definitions across a given Azure DevOps Server collection that includes a conventient bit column (yes/no) for pipelines that happen to make use of the DevSecOps tasks in question.

{{< gist akanieski 726aeef1cb82c173c5930a4a79d86aef >}}

Enjoy!
