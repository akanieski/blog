+++
author = "Andrew Kanieski"
title = "Collecting and Analyzing Dumps with .Net Core on Linux"
date = "2019-12-23"
description = "A short primer on how to collect memory dumps of managed code on linux"
tags = [
    "memory",
    "memory-dumps",
    ".net",
    ".net core",
    "dotnet",
    "dotnetcore",
    "netcore",
    "linux",
    "containers"
]
+++
Over the past 18 years or so of .NET’s life span an entire ecosystem of tools have been developed to assist in troubleshooting application faults and problems. One common tool used to debug is called WinDBG and has historically been used heavily to triage application faults. However, in the past few years, with the advent of .Net Core, more and more .Net applications are running on Linux! This has opened up an entire market in which .Net can be leveraged.  

So let’s paint a hypothetical scenario: you have spent the past 3 months building your product with .Net Core 3.1 and you are using a docker base image of Alpine 3.10.x. You find your application works great, so your team decides its time to ship to production. You deploy your application to your docker host (AKS, ACI, Docker Swarm, etc). You open the flood gates and your users begin to use the application. All seems well until you find your application seems to be locking up.  

Now traditionally you might capture a dump using DebugDiag or perhaps procdump, or you can also trigger it right from Task Manager in Windows. But how might you collect a dump for analysis in .Net Core?  

Much of the information covered here can also be found in an article on GitHub found [here](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-dump-instructions.md). Save it for your reference.  

## Collecting Dumps Using Dotnet Dump

We have a new “global” tool called “dotnet-dump” that can be used to collect and analyze dumps for crashes and memory concerns. It runs both on Windows and Linux and allows you to run SOS commands with the caveat that it is not a native debugger, so you won’t be able to inspect native stack frames.  
So how do we get started? First, as long as you have the .NET Core SDK installed, the tool can be installed right from your dotnet CLI by running the following command:  
``` sh
    dotnet tool install -g dotnet-dump
```
Once installed, all you need to do is identify the PID (process id) of the dotnet application running. You can do this using the following command:  
``` sh
    $ dotnet-dump ps  
    1 dotnet /usr/share/dotnet/dotnet
    2147 dotnet-dump /root/.dotnet/tools/dotnet-dump
```
The above lists the dotnet process as PID #1\. So from there we can request a dump be collected by running the following:  
``` sh
    dotnet-dump collect -p  1  
    Writing minidump with heap to /app/core_20191223_160819  
    Complete
```
#### If you don’t have the SDK installed on your container

Many install just the runtime on their containers to keep their container size footprint down. That being said in order to use this tool you will need the SDK. What you can do in this case is either build a new debug image that uses the “SDK” base image that Microsoft publishes, or you can download the SDK directly into the live container. On Debian 10 I used the following commands:  
``` sh
    $ curl https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb --output packages-microsoft-prod.deb
    $ dpkg -i packages-microsoft-prod.deb
    $ apt-get install apt-transport-https
    $ apt-get update
    $ apt-get install dotnet-sdk-3.1
    $ dotnet tool install -g dotnet-dump
```
At this point the SDK should be installed. Keep in mind that it may not have created the symlink or dotnet-dump might not be added to path. In this case you need to reference it directly at `/root/.dotnet/tools/dotnet-dump`.  

## Analyzing the dump

At this point, analyzing will start to feel more familiar if you are comfortable in WinDBG and SOS. You can analyze the dump collected directly on the container or you can copy it locally. Keep in mind you need to analyze on the same OS/Distro on which you collected, collecting from Alpine and analyzing on Ubuntu was not possible.  
To get started analyzing you can run the `dotnet-dump analyze <path_to_dump>`.  
From here you are provided an interactive console where you can run your familiar SOS commands:  
``` sh
    Usage:
      dotnet-dump [command]

    Commands:
      exit, quit                           Exit interactive mode.
      help, soshelp <command>              Display help for a command.
      lm, modules                          Displays the native modules in the process.
      threads, setthread <threadid>        Sets or displays the current thread id for the SOS commands.
      clrstack <arguments>                 Provides a stack trace of managed code only.
      clrthreads <arguments>               List the managed threads running.
      dumpasync <arguments>                Displays info about async state machines on the garbage-collected heap.
      dumpassembly <arguments>             Displays details about an assembly.
      dumpclass <arguments>                Displays information about a EE class structure at the specified address.
      dumpdelegate <arguments>             Displays information about a delegate.
      dumpdomain <arguments>               Displays information all the AppDomains and all assemblies within the domains.
      dumpheap <arguments>                 Displays info about the garbage-collected heap and collection statistics about objects.
      dumpil <arguments>                   Displays the Microsoft intermediate language (MSIL) that is associated with a managed method.
      dumplog <arguments>                  Writes the contents of an in-memory stress log to the specified file.
      dumpmd <arguments>                   Displays information about a MethodDesc structure at the specified address.
      dumpmodule <arguments>               Displays information about a EE module structure at the specified address.
      dumpmt <arguments>                   Displays information about a method table at the specified address.
      dumpobj <arguments>                  Displays info about an object at the specified address.
      dso, dumpstackobjects <arguments>    Displays all managed objects found within the bounds of the current stack.
      eeheap <arguments>                   Displays info about process memory consumed by internal runtime data structures.
      finalizequeue <arguments>            Displays all objects registered for finalization.
      gcroot <arguments>                   Displays info about references (or roots) to an object at the specified address.
      gcwhere <arguments>                  Displays the location in the GC heap of the argument passed in.
      ip2md <arguments>                    Displays the MethodDesc structure at the specified address in code that has been JIT-compiled.
      name2ee <arguments>                  Displays the MethodTable structure and EEClass structure for the specified type or method in the specified module.
      pe, printexception <arguments>       Displays and formats fields of any object derived from the Exception class at the specified address.
      syncblk <arguments>                  Displays the SyncBlock holder info.
      histclear <arguments>                Releases any resources used by the family of Hist commands.
      histinit <arguments>                 Initializes the SOS structures from the stress log saved in the debuggee.
      histobj <arguments>                  Examines all stress log relocation records and displays the chain of garbage collection relocations that may have led to the address passed in as an argument.
      histobjfind <arguments>              Displays all the log entries that reference an object at the specified address.
      histroot <arguments>                 Displays information related to both promotions and relocations of the specified root.
      setsymbolserver <arguments>          Enables the symbol server support
      soshe
```
### Other Tools

Another fine tool for collecting CPU performance stats of an application is PerfCollect. Historically this has been a “Windows” application. However we have made significant strides in bringing a similar experience to Linux. For more information on this head on over to our article on [collecting cpu samples via a sidecar container.](https://devblogs.microsoft.com/dotnet/collecting-net-core-linux-container-cpu-traces-from-a-sidecar-container/)  
