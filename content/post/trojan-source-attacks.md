+++
author = "Andrew Kanieski"
title = "Spotting Trojan Source Attacks"
date = "2021-11-17"
description = "A simple c# utility for spotting bidirectional unicode attacks"
tags = [
    "security",
    "trojan",
    "trojan-source-attacks",
    "invisible",
    "cve-2021-42574"
]
+++
Recently an interesting attack was uncovered thats been nicknamed **"Trojan Source Attacks"** [(cve-2021-42574)](https://nvd.nist.gov/vuln/detail/CVE-2021-42574). They use unicode characters that are often not rendered in editors and user interfaces leaving developers unaware that a malicious actor has shifted the logic of their application for nefarious purposes. More details can be found published by the analysts who discovered it [here](https://trojansource.codes/).

If you're looking for a way to identify this vulnerability in your code I've written a small utility to spot these unicode characters in your source code. You can plug this utility into our build pipelines to catch these characters before they make it into your source code supply chain. Better yet, plug them into [Pipeline Decorators](https://docs.microsoft.com/en-us/azure/devops/extend/develop/add-pipeline-decorator?view=azure-devops) in Azure DevOps and catch them all across your organization! 

Source code can be found [here](https://github.com/akanieski/bidi-scan).

For more info on Trojan Source Attacks checkout the below link for more info.

```
@article{boucher_trojansource_2021,
    title = {Trojan {Source}: {Invisible} {Vulnerabilities}},
    author = {Nicholas Boucher and Ross Anderson},
    year = {2021},
    journal = {Preprint},
    eprint = {2111.00169},
    archivePrefix = {arXiv},
    primaryClass = {cs.CR},
    url = {https://arxiv.org/abs/2111.00169}
}
```

Enjoy!

**Andrew Kanieski**
