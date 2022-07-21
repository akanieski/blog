+++
author = "Andrew Kanieski"
title = "Automatically Update Azure DevOps App Tier Servers with Latest Pipelines Agents"
date = "2022-07-21"
description = "Automatically Update Azure DevOps App Tier Servers with Latest Pipelines Agents"
tags = [
    "azure devops",
    "devops",
    "build",
    "pipelines",
    "azure-pipelines",
    "azure-pipelines-agent"
]
+++

Some enterprise customers run their Azure DevOps Server behind a secured corporate Web Proxy. At times 
this can make it challenging for Pipelines agents to be able to download the latest Agent versions. 
Fortunately, Azure DevOps Server has a mechanism built in to cache Agent Installs locally on the App Tier
Servers. 

Please find below a script that can be used to automatically download the latest Pipelines Agent zips to your
App Tier servers. You can put this in fact on a nightly Windows Scheduled Task to ensure your App Tier servers 
are providing agents across your enterprise with an opportunity to download the latest agent updates.

{{< gist akanieski 77ef98baa96663657b75bce6fda1236e >}}

