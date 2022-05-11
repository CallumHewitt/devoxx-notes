# The four key metrics unleashed

2022-05-13 14:10
Andrew Harmel-Law
Technical Principal @ Thoughtworks
@al94781

## Origins of the Four Metrics


They came off the back off the DevOps movement, particularly out of the book `Accelerate: Building and Scaling High Performing Technology Organisations` which came from the DORA research programme.

The book did statistical analysis to see if they could predict performance and profitability based on the standard of their DevOps practices at the firm. The better they were at DevOps practices, they were more profitable. There was at least a 2x improvement. This was a causitive way of improving both commercial and non-commercial goals.

Commercial goals

- Profitability
- Market Share
- Productivity

Non-commercial goals

- Quality of goods and services
- Customer satisfaction
- Employee satisfaction

Diagram in photos show the things that were stronger than correlated to DevOps practices. Arrows should what led to what.

But then management might say, how do I know it's working? And that's where the 4 key metrics come in.

## Four Key Metrics

The four metrics that lets devs argue with senior managers to help make their lives better.

1. Deployment frequency
2. Lead time for changes
3. Change failure rate
4. Time to restore service

Thoughtworks have had the four metrics in adopt since 2021.

It turns out setting these things up can be very challenging.

## Steps to the Four Key Metrics

1. Define then locate
2. Capture & calculate
3. Display & understand
4. Own & improve

## Define then locate

### Deployment measurement

Deploying to production does not mean released for customers. We just to make it available for customers at the flick of a switch.

This picture you never see:

Git -> Prod

It's more like

Git -> P
Git -> R
Git -> O
Git -> D

There should be no collaboration between items in these pipelines. Otherwise you're not doing Microservices.

You might actually have

Git -> -> -> PROD

So you have subsequent Jenkins pipelines or something like that. Sometimes blocked by a change management tool.

Git -> -> ->    P
Git -> ->       R
Git ->          O
Git -> -> -> -> D

Or more likely that ^

DORA doesn't care. It just cares about the fact that you have a pipeline from one side to the other.

You may also have...

Git -> \
Git ----> PROD
Git -> /

This happens with split out microservices from monoliths. This requires you to do more work up front to get your metrics.

#### The metrics

The first data point is when the merge is made and dev is complete and ready to go.
The second data point is when the application is packaged and in production.

With fan in changes that contain one change from repo A, two from repo B and three for repo C. This makes it harder as you need to unpack all of the timings.

### Failure measurement

Understanding whether or not your change caused a failure is difficult. You want to define and capture your change failures. 

One way is to have a ticket in Jira or something like that. The failure should be an interruption in the running service that causes difficulties with your clients/customers doing their thing.

Time to restore can be done as to when you close out the ticket that fixes the problem.

## Capture and Calculate

Count the number of JIRA tickets, or failure messages in the slack channel etc.

### Deployment frequency

Easiest to capture.
You need the average per day, over a period of time.
Calculate the daily figures.

For example: The average deployments per day over the last 31 days.

Gotchas:
    - Remember, you can't average an average.
    - Don't miss days out on your queries with days that have zero deployments.

### Lead time for changes

Requires two data points.

- The start time for you deployment
- The end time for deployment.

Don't just report the most recent.

Example: average lead time, over the last 31 days.

Gotchas:
    - Don't average an average
    - Don't include changesets that haven't already been deployed! (Infinite end time)

### Change failure rate

Requires two data points.
Proportion
Number of **resolved** failures divided by the number of deploys

Don't be confused if you end up with a number greater than 100%. This means that there are a lot of failures being generated. You might want to tidy up your logging and your alerting to improve the S/N ratio.

Example: proportion of failures per deploy over the last 31 days

Gotchas:
    - It's harder to accurately identify a change failure!
    - Don't both tying failures to individual deploys

### Time to restore service

Requires two data points
Time period
Elapsed time between service failure and service restoration
Ignore unresolved failures

Example: mean time to restore service for all resolved failures in the last 120 days.

Gotchas:
    - Mean or median? You decide (you want not to hide the bad stuff)
        - What makes the most sense to teach you what you don't know about your system

## Display and Understand

Do the smallest possible dashboard. Think about it as thin slices of values. Increasingly more accurate metrics more often.

Share the definitions to make it clear to everyone what you're defining as a failure.

v0.1 - Manual everything
v1.0 - Automated data collection & storage.
v2.0 - Automated dashboards
    - There are third party dashboards available
        - Google's FourKeys
        - Thoughtworks' Metrik
        - Various plugins for Azure DevOps and the Atlassian Suite
        - Note that these rely on certain integrations being available.
    - If you write it yourself you'll be able to do the cool stuff yourself.

All of the extra things from Pete and Andrew's on UI came from the weekly meeting when they got feedback as to what people in that meeting needed to see.

## Own and improve

Over time the dashboard continually improved. They were available at any time but shown monthly in a meeting.

There were no targets to meet a certain goal, but by having the metrics there they become a discussion point and something to base coversations about the improvements you want to make to the platform on.

## Becoming Elite

Elite

- On demand (multiple deploys per day)
- Lead time for changes < 1 hour
- Time to recovery < 1 hour
- Change failure rate 0-15%

## Warning - The Four Key Metric aren't enough on their own

Product Metrics (Build the right thing)
+
The four metrics (Build the thing right)

## Summary

Book - Software Architecture Metrics
