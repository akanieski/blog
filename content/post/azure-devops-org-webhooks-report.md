+++
author = "Andrew Kanieski"
title = "Azure DevOps - List All Service Hooks by Project"
date = "2021-06-30"
description = "A quick and easy script for listing all Service Hooks in an organization by project."
tags = [
    "azure devops",
    "devops",
    "webhook"
]
+++

Recently I worked with a customer to apply proxy settings to the Azure DevOps Server's App Tier components, 
allowing them to route outbound HTTP traffic through their company's corporate web proxy. 

But what happens to Service Hooks that post HTTP requests to traffic that does NOT need to be routed through 
the corporate proxy? Well as stated in my previous article, you can configure a list of HTTP endpoints that 
do not need to pass through proxy.

But how do you know what URLs are being posted to out in the wild?

Below I've written a quick script to pull all Service Hooks by Project and pop them into a CSV. With this you 
can configure any urls that would need to bypass the corporate proxy.

[Source Code Found Here](https://github.com/akanieski/ado-reports/blob/02fcd38c130a959cd0cd9f0219c601e250247968/Get-WebHookUsageReport.ps1)
