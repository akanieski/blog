+++
author = "Andrew Kanieski"
title = "Remediation of old versions of Visual Studio Isolated Shell"
date = "2019-02-26"
description = "Remediation of old versions of Visual Studio Isolated Shell"
tags = [
    "visual studio",
	"isolated shell"
]
+++

Premier Support for Developers is used in all manner of ways to support your organization's needs. Sometimes we work with IT organizations to help in updating or removing older, out of support versions of our software from production across entire enterprise landscapes. At times this means working with hundreds of thousands of computers.


In some cases it can be a challenge to identify what software is installed and present on machines across customer networks. Today we have one specific example, Visual Studio Isolated Shell. If you're wondering what VS Isolated Shell is, take a quick read through this primer.


Why are old versions of VS Isolated Shell a challenge to upgrade/remove? VS Isolated Shell is just a component of a product, used by Microsoft and other third party vendors, to write software. That being said, if you are like many enterprise customers, you might not have a firm grasp of what products are leveraging this component. 


For Microsoft products the answer is easy, we use VS Shell with SQL Server Management Studio, Visual Studio, just to name a few products. But there are an untold number of vendors that could also be using VS Isolated Shell.


### How do you identify what software dependent on Visual Studio Isolated Shell?
At first, it was a challenge to identify consistent methods for identifying dependency on VS Isolated Shell. Some applications will register in the registry, their dependence of VS Isolated Shell. But this is not reliably done by third party vendors as it is not a required step for installation.



After research with our product engineering teams we realized a simple way of identifying what software is dependent on `Visual Studio Isolated Shell`. During the course of operation, an executable that is dependent on VS Isolated Shell will have a `.pkgdef` file alongside the executable.



So let's say you have `MyCustomApplication.exe`, if this application is dependent on VS Isolated Shell there will be a `MyCustomApplication.pkgdef` file alongside the executable.



With this little tidbit of information, we were able to write a powershell script that iterates through a given folder recursively and identifies executables that are dependent on VS Isolated Shell.



Armed with a list of software that may be outdated or out of support, enterprise customers can more easily identify what software needs to be upgraded or removed based on older versions of VS Isolated Shell.



