+++
title = "Agentic AI – Is it ClickOps 2.0?"
date = "2026-03-12T09:00:00-04:00"
description = "Agentic AI promises to execute complex workflows from natural language prompts. But are we drifting back toward ClickOps?"
tags = ["AI", "DevOps", "Agentic AI", "Infrastructure as Code"]
categories = ["Engineering"]
draft = false
+++

Over the past year or so we’ve seen a surge of tools promising *Agentic AI*. The pitch usually sounds something like this:

> Give the AI a goal, and it will figure out the steps to accomplish it.

Deploy infrastructure, fix a broken pipeline, stand up a new service. Instead of writing scripts or infrastructure definitions, you describe the outcome and let the agent figure out the process.

On the surface, this sounds like a massive leap forward.

But it also feels strangely familiar.

## A quick trip down memory lane

Before Infrastructure as Code became common practice, a lot of cloud infrastructure was built directly in web portals. You clicked here, added a resource, set a configuration, and hit save.

During the 2010s I was helping enterprises migrate to the cloud, and for me that mainly meant Azure because that was where my clients were already invested. Many organizations were in a mad dash to hop on the cloud bandwagon, and speed usually won over design.

That rush led to fragile cloud systems that were not well thought out. But at least back then, the same people were often the ones clicking around in the portal, and they maybe remembered how they had configured their resources.

As environments grew larger, that approach started to break down. It became difficult to track what had changed, harder to reproduce environments, and nearly impossible to audit reliably.

Eventually the industry gravitated toward declarative infrastructure using tools like Terraform, ARM/Bicep, and CloudFormation. Infrastructure moved into version control, deployments became repeatable, and environments became easier to reason about.

We even coined a name for the old way of doing things: **ClickOps**.

## Enter the agents

Agentic AI flips the model again.

Instead of writing configuration code, we provide intent through natural language: “Deploy a secure Kubernetes cluster with monitoring enabled,” or “Fix the failing CI pipeline and redeploy the service.”

To be clear, Agentic AI is not inherently the problem. In many cases an agent can absolutely do the right thing by generating Infrastructure as Code, opening a pull request, and flowing through the same CI/CD guardrails we already trust.

The risk appears when guidance is vague and enforcement is weak. In that situation, the non-deterministic nature of LLMs means the agent might choose the path that looks fastest in the moment instead of the one that is most maintainable over time.

Which raises an interesting question: are we moving back toward ClickOps, just with a very capable robot doing the clicking?

## The reproducibility problem

One of the major advantages of Infrastructure as Code is determinism. Given the same configuration, the same result should be produced every time.

Agent-driven workflows can be different.

Two similar prompts might produce slightly different solutions depending on context, model behavior, or changes in the surrounding environment.

That introduces practical concerns around auditing what changed, reproducing the same environment later, and rolling back when the agent makes a questionable decision.

A concrete example is an agent that decides to use Azure MCP directly to modify resources in real time rather than producing Infrastructure as Code and letting CI/CD apply it. Even if the result "works," we have effectively slipped back into ClickOps, except now the clicking is abstracted away and easier to miss.

These are problems DevOps teams spent the last decade solving.

## Where agents actually shine

That said, Agentic AI is genuinely useful when applied in the right places. It is great for generating infrastructure templates, scaffolding CI/CD pipelines, troubleshooting operational issues, and automating repetitive engineering tasks.

In these cases the agent acts more like a pair engineer than a replacement for the system itself.

## The sweet spot

The workflow that seems to work best is straightforward: use agents to propose solutions, convert those solutions into declarative infrastructure or code, commit the result to source control, and let automation handle execution. Just as important, enforce that workflow with policy so agents cannot bypass it during moments of convenience.

Agents become a powerful design assistant, but the authoritative definition of the system still lives in version-controlled code.

## Final thoughts

Agentic AI is a powerful tool, but convenience has a way of creeping into engineering workflows.

If the past decade taught us anything, it’s that **repeatability beats convenience over time**.

The practical takeaway is to keep your agents on the rails. Avoid giving them freedom to make direct-touch changes on real cloud systems whenever an IaC path is available.

Take the time to build strong instructions, prompts, and skills that consistently steer the agent toward Infrastructure as Code outputs that can be reviewed and deployed through CI/CD.

Agents may help us design systems faster, but keeping the blueprint in code is still what keeps those systems understandable.
