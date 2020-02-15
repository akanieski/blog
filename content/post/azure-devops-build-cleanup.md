+++
author = "Andrew Kanieski"
title = "Managing 'Cleanup' Jobs Being Processed by Your Agents with Azure DevOps"
date = "2019-06-09"
description = "Managing 'Cleanup' Jobs Being Processed by Your Agents with Azure DevOps"
tags = [
    "azure devops",
    "devops",
    "build",
    "cleanup"
]
+++
Managing your software development lifecycle in an enterprise environment can at times be a challenge. For many large enterprise customers a commonly used feature is the build retention policy, where build artifacts are retained for a period of time after your build pipeline successfully generates artifacts.


But what happens when your build artifacts begin to exceed your retention policy? `Azure DevOps` has a clean up process that daily executes to clean up old build artifacts. It is scheduled to run daily at 03:00 AM UTC. This process is handled and executed on the actual build agents.


At times you may notice these clean up jobs queued in your build queue. By default `Azure DevOps` will leverage the build pool originally used to build the artifact. Some customers have found it advantageous to designate a separate set of agent to process these jobs, so as to not have them interfere with the existing jobs.


In order to achieve this you can take advantage of the `Build.Cleanup` capability. By adding this capability to a build agent, it will designate that agent as the build agent to handle the clean up process. Keep in mind that the build agent will need access to any of the network shares in which build artifacts are residing. 