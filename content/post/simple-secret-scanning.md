+++
author = "Andrew Kanieski"
title = "Quick and Easy Secret Scanning Using Git Hooks and Git Leaks"
date = "2023-06-22"
description = "A quick and easy secret scanning solution for your personal projects"
tags = [
    "git",
    "devsecops",
    "secrets",
    "secret-scanning",
    "pre-commit",
    "githooks",
    "gitleaks"
]
+++
Have you ever mistakenly committed a config file or environment file that contains 
secrets or other sensitive data? Do you hate having to rewrite your Git History every 
time you make such a mistake?

What if you could have auto-magically spot secrets before you commit your code to your 
git repo?

Here is a simply way to block commits from your workstation that costs nothing and is 
very simple to setup.

### Requirements 
- Podman or Docker (Docker required for Windows)
- Access to ghcr.io where GitLeaks keeps its container images

### Setup 
Create a file in your `.git/hooks` directory called `pre-commit`. The `.git/hooks` 
directory provides devs with a hook system that allows them to specify executables to run
at various points in the git workflow. More details can be found on Git Hooks 
[here](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks). 

In our case we will use the below script. The first thing it does is determine which OCI 
solution is available on your workstation. It checks the path for `podman` first, and if 
podman is not available, then it will check for docker. If neither are available, you will 
need to get the gitleaks executable onto your machine and replace the container run 
command with a call to the local gitleaks executable. (Perhaps I can show this on a later 
post). 

After determining the best way to run the gitleaks container image, it goes ahead and runs
the gitleaks container image with the repo directory mounted. It lists out results directly 
in the console by default. After running it makes sure to capture the exit code from the 
container run and add logging and exit with either 1 if a secret is found, or 0 if no 
secrets are found. The Git Hooks system is designed to abort the commit if the pre-commit 
hook exits with a non zero exit code.

Now that the `.git/hooks/pre-commit` file has been created you can go ahead and make sure 
the pre-commit hook is executable using `chmod +x .git/hooks/pre-commit`. 

{{< gist akanieski d778dadd8d5159e721ad39a90e948252 >}}

See below the results of a healthy commit:

![Healthy Commit](/gitleaks-hook-01.png)

And now what it looks like when it spots a secret in your commit:

![Bad Commit](/gitleaks-hook-02.png)

And what if there are false-positives? GitLeaks supports a `.gitleaksignore` file that 
allows you to copy the fingerprint of the secret and add it to the ignore list.

For more information on GitLeaks, head over to their 
[GitHub repository](https://github.com/gitleaks/gitleaks) and check it out! 

### Downsides?
It was easy to setup, but the draw-back to using a git client hook is that the hook itself 
is never committed into the repo. It exists only locally on your workstation and has to be 
manually shared or scripted into existence to be used by other developers. So if your looking 
to bring this secret scanning to other team members, or worse across your entire organization 
then this solution is not the right fit. But what is?

A variety of software vendors now provide services that scan your source code for secrets, 
some will do this passively (see [GitHub Advanced Security](https://docs.github.com/en/get-started/learning-about-github/about-github-advanced-security) now available for both GitHub and [Azure DevOps](https://azure.microsoft.com/en-us/products/devops/github-advanced-security) customers, some will even block commits 
to the vendor hosted repos if they contain secrets. But all of these services require a 
financial investment. For personal projects or for small businesses that do not want to 
fork over the cost for such services, this approach may work great for you.
