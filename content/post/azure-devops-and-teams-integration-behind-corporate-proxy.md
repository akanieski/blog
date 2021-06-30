+++
author = "Andrew Kanieski"
title = "Azure DevOps and Teams Integration Behind a Corporate Proxy"
date = "2021-06-30"
description = "Andrew Kanieski and a colleague Vito Lodese dive into Teams Integration behind a corporate proxy server."
tags = [
    "azure devops",
    "devops",
    "proxy",
    "teams",
    "chatops",
    "microsoft teams"
]
+++

Chat based collaboration and workstream are all the rage in today's largely remote workforce. Azure DevOps is no stranger 
to this need and as such Microsoft announced back in 2017/2018, newly added support for Azure DevOps integration with 
Microsoft Teams via Service Hooks. With this new integration developers can configure service hooks that notify channels 
in Microsoft Teams of various events from code pushes and pull requests to build output and releases. This helps keep the 
development team informed throughout the development process.

Excited to try it out, my colleague, Vito Lodese and I recently worked to bring this tool to a large enterprise customer of 
Azure DevOps. If you look at the Microsoft documentation, setting it up is pretty straight forward. We were quickly able to 
set it up in Azure DevOps Services with little to no friction. But what about customers running Azure DevOps Server that 
are behind a corporate proxy?

Looking across the web you can find lots of documentation on configuring Pipelines agents for corporate proxy. But requests 
that are sent to service hooks do not come from pipelines agents, they are issued by the Azure DevOps App Tier servers. That 
being said how do we configure the App Tier to respect a corporate proxy?

The App Tier servers of Azure Devops have several main components, two of which are the "Background Job Agent" and the 
"Web Services". Each of these are .NET based applications and as such can be configure much the same as any .NET application.
Details on this configuration can be foudn [here](https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).

In your Azure DevOps Server installation path you will find two configuration files that will need to be updated. First, lets
update the `Web Services` component with it's configuration located at `C:\Program Files\Azure DevOps Server 2020\Application Tier\Web Services\web.config`.
We will need to add a new section directly under <configuration> so it should look much like this:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  ...
  <system.net>
    <defaultProxy useDefaultCredentials="true">
        <proxy usesystemdefault="True" proxyaddress="CORPORATE PROXY URI" bypassonlocal="True"/>
    </defaultProxy>
  </system.net>
</configuration>
```
Make sure to avoid having *two* <system.net> sections in case one is already present. Restart the two application pools for 
Azure DevOps Server from the IIS Management Console.

Next you will need to make the same change to the `C:\Program Files\Azure DevOps Server 2020\Application Tier\TfsJobAgent\TfsJobAgent.exe.config`. 
Restart the Background Job Agent via the Services Management Console. Given a few moments you can go ahead and configure the Service Hook for 
Teams notifications.

One last thing to note: Some customers will find that some users have configured Web Hooks that post notifications to URLs that *do not* need to be 
routed through the corporate proxy. In such cases you may use the `<bypassList>` node under `<defaultProxy>`. More details on how to configure this 
setting can be found [here](https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/network/bypasslist-element-network-settings).

