# Embracing Collaborative Chasos- Running Chaos Days on Large Platforms

2022-05-13 09:00
Lyndsay Prewer
@lyndsp
@equalexperts

## What is chaos engineering? Why should we care?

Production incidents happen. It's not if, it's when.

We can build large-scale services very quickly. We can process large amounts of data and control lots of different devices. We can work at planet-scale very quickly.

Google Cloud and Amazon Cloud do have issues. An outage in Google Cloud stopped people's Nest systems stopped working. You couldn't get into your home or change the temperature.

"Two normally-benign misconfigurations **and** a specific software bug, combined to initiate the outage"

Three things combined to cause this unforseen problem. Often it's more than one system that causes the issue.

### How can your system fail?

- What are the component parts?
- How are they connected?
- How reliable is each part?
- How reliable are the connections?
- What happens when X fails?

We can address risk by deliberately inducing failures. Observe, reflect and improve. Build resilience in (like quality), and think about production (and failure) all the time.

### "Resilience"

1. The capability to recover quickly for difficulties
2. The ability of a substance or object to spring back into shape; elasticity

### Four Forms of Chaos Engineering

1. Manual Chaos
    - Chaos Days - What is going to be broken is picked ahead of time
    - AWS Game Days
    - Change specific chaos - Look at a change going in. Think about how it could fail and put in design decisions to deal with those failure modes.
2. Automated chaos
    - Chaos monkey - Run failures on your network simulating all kinds of problems
    - AWS spot instances / GCP Preemptible VMs - Your VMs can be killed at any time. So effectively you're building in chaos engineering because you have to be able to deal with expected failures
    - Randomised pod killer - Hackathon result. Mario game that randomly kills instances on a kubernetes cluster
3. In process chaos engineering
    - Part of normal engineering process
    - Focus for all roles in the team
    - Production thinking/building resilience in
4. Unplanned chaos
    - Every days is a school day
    - Handle incidents well
    - Learn from incidents - and have post incident reviews
    - Start simple, then incorporate tooling
    - We get a lot of "what was the root cause and how can we make sure it never happens again?"
        - It's very rare to get the same root cause over and over again. Different things combine in different ways. So we get the most of post incident reviews by learning how people diagnose and respond to issues. What can we build in to fix that?

### How does this help?

People

- Knowledge - for both responding and for designing your systems
- Behaviour
- Expertise

Product

- Simplification
- Observability
- Runbooks
- Resilience

Process

- Managing incidents
- Learning from incidents
- Engineering approach

## Running a Chaos Day - When and How?

The context system is a public facing system with 60 delivery teams.
100 million internal requests on their business day.
100 million log messages on the busiest day.
~ 1000 Microservices
6 Platform teams (AWS PaaS)

2013 - Platform was built. Cloud, Docker, Scala, Mongo, ELK
2014 - Fast growth (teams, services, traffic)
2015 - Multi active WIP
2016 - Multi active enabled

All of this time they were having unplanned chaos days.

2017 - More multi active (to AWS). Self serve deploys to production. Now anyone can deploy to prod at any time.
2018 - Move to AWS

Now we're ready for Chaos. Shortly before the January major business event.

### Who, what, when, where?

Started working with a closed team. The experts from each of the 6 platform teams. These were the ones the teams went to when things went wrong (highest bus factor).

It's also useful to do all of this in the open where you involve as many people as possible. From designing the process and the experiments you learn a lot too.

### Chaos Scope

Know the architecture
Know your steady state
Know your constraints

- What's in your control?
- What's not?
- What needs protecting?

Some systems will be out of scope, others you know should be able to gracefully come up. There might be legacy components you need to protect to and make sure you don't accidentally DDoS them.

### Goal

Come up with 10-20 experiments. What are you really worried about happening? What could we do that would help us learn about the resilience of our platform?

Now those experiments need to be prioritised and make a card for them:

- What failure are you invoking?
- What impact are you expecting that failure to have?
- What response are you expecting?
- How will the failure be rolled back?
- Other Chaos to do in conjunction - link to other cards.

Chaos days are also a great way to learn about the security of your system. Simulate both external and internal attacks. How long can people go on your platform unnoticed before they get seen? How much damage could they do?

### Decide where to run them

Production, or **closest** to it. Netflix forced their engineers to design better by randomly shutting services down. But they put a lot of thought into it.

We need a production like load and telemetry. We also need to decide the blast radius and the comm's channels.

### Decide when

You need to decide whether or not to warn the teams. But you need to make sure regardless that you know what else is going on. Is anyone close to critical delivery etc.? When do you stop causing chaos and roll back the failure states?

Often a warning will be saying we're going to cause chaos, but not tell them exactly what experiments they're going to run.

## Execution

Either physically or virtually co-locate the chaos agents. Have one person facilitate.
Collaborate and coordinate well. Time-box the experiments so that you cover the most ground. If teams haven't noticed your failure state, move on.
Document well. Who noticed what when? How did they recover? etc.

For everyone else, make sure they know to treat this as just an ordinary day. They need to pretend that any failures are like production failures and do their usual production failure response patterns.

## Retrospection

Split the post-mortem process. Each team should run their own retrospective and look at what the issues they responded to were, and how they reponded, how did their processes work and are there any change they can make to make the system more resilient.

Also examine the chaos process. What can we do better next time to make it easier?

Make sure to plan the retro time ahead of time so that people have the time to do post-incident reviews while the incident is still fresh.

Write the review well, make people feel gripped by it. And then socialise them to as many people as possible so they all gain the knowledge.

## What did we learn?

- Start small - maybe only run 5 experiments on a single day
- Manage/limit the pain - don't inflict the most pain, learn the most things. What parts don't you know about?
- Production-like is good enough.
- Focus on learning, not fixing
- Have fun!

## What next?

- Where are you at in the journey?
- What's the next (baby) step?
- Need any help
    - Talk to us @equalexperts
    - Check out their playbooks

## Questions

How did you convince the business?

- Cost of unplanned chaos vs planned chaos.
- Note what time commitment this would take
- Map it to what the stakeholders should be worried about
- They had an opportunity to give the stakeholders more confidence that the upcoming big business day would go well

What kind of failures did you induce?

- Fill up a disk
- Take down one of the networking components
- How well does the AWS self-healing work? What happens if we kill different instances?
- Breaking parts of the telemetry. Where else do people go for info?
- Cause problems for the CI/CD system. Infrastructure as code was everywhere. If you have to completely rebuild you CI/CD what happens?
- Breaking authentication, how does the team owning that system deal with it? Only one of the agents of chaos knew how to fix that one as it turns out

Use the same comms channels for production issues? Why? What happens if a real live incident happens?

- When they used pre-prod environment it was causing failures that needed to be visible to know how they would respond to a real outage.
- In the military when actual things go bad during real things, they announce a safeguard to make sure that people know it's no longer an exercise.

What about chaos on the comms system themselves?

- They used Slack so it was difficult to break.
- Slack had some failures lately so they got some free unplanned chaos days out of that.
- Having an agreed backup.
- They simulated confluence going down and all of their runbooks were there. So people couldn't access their runbooks.
