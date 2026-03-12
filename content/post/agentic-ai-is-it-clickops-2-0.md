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

This worked fine early on, but as environments grew larger the approach started to break down. It became difficult to track what had changed, harder to reproduce environments, and nearly impossible to audit reliably.

Eventually the industry gravitated toward declarative infrastructure using tools like Terraform, ARM/Bicep, and CloudFormation. Infrastructure moved into version control, deployments became repeatable, and environments became easier to reason about.

We even coined a name for the old way of doing things: **ClickOps**.

## Enter the agents

Agentic AI flips the model again.

Instead of writing configuration code, we provide intent through natural language: “Deploy a secure Kubernetes cluster with monitoring enabled,” or “Fix the failing CI pipeline and redeploy the service.”

The agent then determines the steps required and executes them.

Which raises an interesting question: are we moving back toward ClickOps, just with a very capable robot doing the clicking?

## The reproducibility problem

One of the major advantages of Infrastructure as Code is determinism. Given the same configuration, the same result should be produced every time.

Agent-driven workflows can be different.

Two similar prompts might produce slightly different solutions depending on context, model behavior, or changes in the surrounding environment.

That introduces practical concerns around auditing what changed, reproducing the same environment later, and rolling back when the agent makes a questionable decision.

These are problems DevOps teams spent the last decade solving.

## Where agents actually shine

That said, Agentic AI is genuinely useful when applied in the right places. It is great for generating infrastructure templates, scaffolding CI/CD pipelines, troubleshooting operational issues, and automating repetitive engineering tasks.

In these cases the agent acts more like a pair engineer than a replacement for the system itself.

## The sweet spot

The workflow that seems to work best is straightforward: use agents to propose solutions, convert those solutions into declarative infrastructure or code, commit the result to source control, and let automation handle execution.

Agents become a powerful design assistant, but the authoritative definition of the system still lives in version-controlled code.

## Final thoughts

Agentic AI is a powerful tool, but convenience has a way of creeping into engineering workflows.

If the past decade taught us anything, it’s that **repeatability beats convenience over time**.

Agents may help us design systems faster, but keeping the blueprint in code is still what keeps those systems understandable.
