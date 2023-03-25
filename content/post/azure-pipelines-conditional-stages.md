+++
author = "Andrew Kanieski"
title = "Run Azure Pipeline Stages Only When Certain Files Change"
date = "2023-03-25"
description = "A look at how you can conditionally run stages only when files of a certain type or path are changes"
tags = [
    "azure devops",
    "azure pipelines",
    "devops",
    "cleanup"
]
+++
**Table of Contents**:
- [Identify files that have changes](#how-to-identify-what-files-have-changed-on-a-given-commit)
- [Conditionally trigger a stage](#how-to-conditionally-trigger-a-stage)

Recently, I worked with a customer with an interesting challenge. They had spent a considerable amount of time implementing a robust Azure Pipelines template that allowed their operations teams to standardize their Pipelines across their enterprise.

One of the key solutions this pipeline provided was an opportunity for their database management team to control 'Environments' in Azure Devops. This database management team could enforce a variety of controls on their environments to ensure the safety and security of their shared database clusters. For example, these environments all required approvals before database changes could be executed in their environments. The team quickly was able to implement this requirement!

All is well in database-land, right? Well, as many can attest, sometimes well intentioned features can become more of an obstacle than a feature. Very quickly, app teams would notice that *all* of their deployments would suddenly require approvals. Why? 

In this implementation, deployments to a given environment were all executed within a given stage. That stage contained database deployment jobs that would *always* trigger approvals, even when there were no database changes specified.

So, how can we address this issue? Let's break it down into smaller problems.

#### How to identify what files have changed on a given commit?

Fortunately, `git diff` is a very simple command to grab a list of changes in a given commit or range of commits. You can specific the current commit using `git diff --name-only HEAD~ HEAD`. This will yield you a `newline` delimited set of files that were changed. 

But what if my work is spread among many commits? This problem can be solved by triggering our deployments on Pull Requests back to `master`. When we make pull requests we have a variety of merge strategies. Both `Basic (no fast-forward)`, `Squash` and `Semi-Linear Merge` will result in a final merge commit that contains the various changes made. 

This means we can simply `git diff --name-only HEAD~ HEAD` and we'll be able to get a list of all the files changes on the PR.

Below, I've wrapped up this `git diff` command with some extra bit of scripting as a re-usable task template. It allows you to specific a `searchPattern` to identify when SQL files have changed. And it also gives you the optional opportunity to publish the changes as an artifact.

One key parameter that this task template requires is the `setVariableNamed` parameter. This indicates where you would like to store the results of our file search. If the commit contains changes that match the given pattern then we set a variable of the given name. This variable will be consumed later on in the pipeline. Official docs on how this works can be found [here](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/set-variables-scripts?view=azure-devops&tabs=bash).

{{< gist akanieski "902b7c17d6da50be2ef79f3a02ab5b3d" "analyze-changes.yml" >}}

#### How to conditionally trigger a stage?

Now that we have a template task that can identify changes to SQL files, we can go ahead and build out our pipeline. 

Below our in our example we have a two stage pipeline. As part of the first stage we identify what files were changed, using our newly created task template, listed above. 

Then we move on to our "conditional stage". Notice the use of the `condition` on the stage's definition. `condition: eq(dependencies.AnalyzeStage.outputs['FirstJob.Changes.SqlChangesFound'], 'Yes')` Here we are identifying that our stage is only to be execute if the outputs of the `AnalyzeStage`'s `FirstJob.Changes.SqlChangesFound` is equal to `Yes`. Notice the syntax used here. More details on this can be found in the [official docs](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/conditions?view=azure-devops&tabs=yaml%2Cstages#use-the-output-variable-from-a-job-in-a-condition-in-a-subsequent-job).

Additionally, its not just stages that can be made conditional, but jobs themselves.

{{< gist akanieski "902b7c17d6da50be2ef79f3a02ab5b3d" "azure-pipelines.yml" >}}

Enjoy!

**Andrew**
