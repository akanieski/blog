+++
author = "Andrew Kanieski"
title = "Searching for large files in TFVC"
date = "2021-04-22"
description = "A simple way to track down large files in TFVC"
tags = [
    "azure devops",
    "devops",
    "tfvc",
    "cleanup"
]
+++
Has your Azure DevOps Server collection database grown so large it's become unmanageable? Tired of your database backups running long? 
Do you suspect your users have been storing all their favorite cat videos in their Team Project's TFVC repository? 

It's all too common for developers to mistakenly store large binary files in source control thinking it's a good place to store large 
binary files. Although there are scenarios this might make sense, generally speaking it isn't a great idea, it bloats your collection 
databases, slowing down backups, making database replication seeding slower and eats up drive space when there are better long term 
storage for such data, like S3 storage.

A fine example of a large binary objects I've seen sprinkled throughout Azure DevOps database is the Java SDK installer, MS VC++ 
Redistributable Installers, and things like these. Inadvertantly developers may upload these in Azure DevOps for safe keeping. Little 
do they know that a dozen other developers in your organization have all uploaded the exact same installer in the database, duplicating 
for now reason hundreds of gigabytes of data. If left unchecked your database can grow wildly out of control.

### How to find the largest files in your TFVC repos

The below script can easily be ran against your collection db to locate the largest files, the project they reside in, the changeset 
they were committed in and when, and who was the brilliant minds behind upload such a file in your precious Azure DevOps data. :-) 

{{< gist akanieski 5cf1ab9045fe7adab5457aa4db6df14d >}}

The only thing left is to actually destroy the files. Head over to our [latest docs](https://docs.microsoft.com/en-us/azure/devops/repos/tfvc/destroy-version-controlled-files?view=azure-devops) to read about the `tf destroy` command. Don't forget 
to that your database will not automatically remove the files immediately after destroying them. There is an automatic clean up job that 
runs within a week that will clean up the file metadata from your database. To force this cleanup use the `/startcleanup` flag when you 
destroy the file in question.

After you've done a solid round of cleanup, this doesn't necesarrily reduce the database size on disk since the database will still 
retain it's overall size after the data is destroyed. You will need to run [DBCC SHRINK](https://docs.microsoft.com/en-us/sql/relational-databases/databases/shrink-a-database?redirectedfrom=MSDN&view=sql-server-ver15)
to ensure the database reduces it's size footprint on disk.

Enjoy!

- Andrew
