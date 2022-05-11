# The Open Source Effect

2022-05-13 11:20
Benny @ GitHub on Copilot
Java Champion
OSS Enthusiast
@bmuskalla

## It started with an Exception

One of the participants in a training encountered an Exception. They couldn't fix it because it was in a framework he didn't know. He had a mental barrier between their code, and the code that you didn't own.

This started the idea of this OSS talk. How does it change your mindset and impact your career.

## Breaking into OSS

- Learning & Fun
    - OSS can be fun. It also has a dark side.
- New Opportunities
- Help your company
- Help your career

How can these things set you up for success.

## Two Paths

1. Contribute
    - Like going to a city and meeting the people there.
2. Maintain
    - A lot harder but an amazing adventure if it works.

## First OSS project

Working with PHP back in school. There was a database called existdb, it was an XML database. This was before NoSQL was popularised.

He wanted to write a client for that database. The project proposal was submitted to pear, the PHP package manager. Then the community votes on that proposal.

It took a year for the proposal to be accepted. There were 4 total downloads.

Learned how to:

- Write a project proposal
- Properly document APIs
- Dynamically switch between APIs

One day it was featured in a book. No one read it but it was featured.

Lesson 1: Success is defined by what you learned from it. OSS can provide the environment for that. Especially with the community around it.

CEO @ Bredex - Alex Schladebeck
    - Taught the value of community working together on things

## Eclipse

Benny raised a bug with Eclipse. The bug was closed as invalid but he learned why.

The contributors explained how to correctly use the tool.

Benny became a contributor and accumulated nearly 1000 defects resolved.

The second one - A discussion was had with Robert Elves and Steffen Pingel. The report turned into a 9 year career at a company that Robert and others working on that ticket founded.

Principal Software Engineer @ Tasktop
    - Working on Mylen led to the company

Lesson 2: Contributions are more than code. Learn from interactions with the community.

## First code contribution to a large project

Issue number 4 on Eclipse annoyed Benny so much that he decided to try and fix it. Lesson 2 and a half - write proper bug reports. Be nice to open source people.

The assignee came back saying that the project was easy to fix. It took two days to get the project set up locally. 

"The hardest part of any first-time contribution to an open-source project is figuring out how to run and build its tests".
    - If this is hard. Help them make it better.

This three line code change was Benny's first line of Java. It had a bug that's still there but there we go.

CTO @ Cloud Native Computing Foundation (CNCF)
    - Able to get their first internship contributing to OSS which led to a job at IBM working with the Eclipse community

Lesson 3: Work on things that interest you. Your own toolchain is a good start.

## Benny, I have better things to do with my evenings...

Unblock your company.
    - Create awareness about how your products depend on OSS
    - Cut costs by avoid workarounds. Contribute to OSS and fix them directly.
    - Bug fix for OSS project = +3 hours of your time, far less work around code.
    - Now you can contribute to OSS at work!
    - Mark @ Gradle's first OSS contribution was to JUnit 4 doing a version bump dependency fix that required a new version for his company. No one wanted to cut a new release so he did it. Now he was the lead of JUnit 5.

Lesson 4: Make your work visible.

Your blog posts and OSS contributions are visible which allow you to showcase your work.

## Gain Expert Knowledge

Eclipse search box always used to do the wrong thing. On the wrong page, searching the wrong buttons.

Digging into the code, he found that the things he wanted to fix were already fixed. There's a customise button that fixes all of the issues.

A few months later, they did a talk about IDE best practices. 20 people showed up afterwards and thanked them for showing them the fix.

There are also hidden easter eggs undocumented in Eclipse like Ctrl + certain buttons that aren't documented anywhere.

It might reveal certain secrets of the code.

Guava's factorial method hardcodes up to 20 factorial because after that it overflows.

Asking the maintainers (with appropriate reproducible bug reports) and helping out on forums irc/gitter/slack helps you gain knowledge.

Principal Engineer @ Heroku (David Zuelke)
    - Agavi contributions focussed on solid strucutre, TDD which enforced best-practices for the developers that use it.

Getting familiar with a different codebase, how do they do testing differently. The worst thing that happens is that you learn something.
    - Navigating on Github with code search ("." on a repo/PR) lets you navigate with an IDE like interface.

Going in and changing things and letting the tests guide you is a good way to experiment and learn. Make the change because no one is watching!

- What frameworks do they use?
- How did they set up their build?
- Why are those tests so fst?
- How do they triage issues and PRs effectively?

Ask the "5 Why". 

CEO @ Hyperpersistence - Vlad Mihalcea
    - Studying the source code helps them learn software patterns and learn ingenious solutions to common problems.

Lesson 5: Get your hands dirty. The worst that can happen is you learn something new.

## Open issues blowing up

Running an open source project means that those that use it have ideas about how to evolve it. The more successful you are the more people who have different interests and ideas and want to contribute.

Often there then end up being not only too many requests, but conflicting requests.

At Gradle 2500 issues for 6 people is too much. They need to get that into a state of what is reasonable, and what is important. It adds a management overhead to your project.

They wrote a bot that removes open requests that haven't been touched for 2 years. This was unpopular but if a request is stale, then that's fine. If it's not it can be reopened two weeks later. This closed 1000 issues, but 100 were reopened as the bug is still valid. This reduced the noise and improved the signal.

Helping OSS by going in and trying to reproduce a report can help increase the signal for the maintainers.

You can help projects:

- Proper issue templates
- Reproduce reported defects
- Help with triaging issues

Product Security Engineer @ Github - Leila Alderman
    - Fixing some typos and rephrasing the grammar in the README of the Odin project.
    - Ended up being top 10 contributor when doing non-code contributions

Lesson 6: Help maintainers by creating space for their own contributions

## Sponsoring

GitHub sponsors or Buy Me A Cup of Coffee are good.

Google started a summer of code where young people can apply to work on OSS projects. The students get paid by Google.

Patience Shyu - Senior Backend Engineer @ Octopous Energy
    - Did the summer of code program and the connection to the mentor has helped so much

Sponsorship is not the same as Sponsoring. It's the idea of using your connections and influence to do things. Actively helping others using your connections.

https://larahogan.me/blog/what-sponsorship-looks-like

97 Things Every Java Programmer Should Know had different people contributing to the same book. 

In 2010 Benny was before University. He was hanging around the Eclipse IRC. Eclipse Con happened and the tickets were very expensive and it's in California.
    - Someone named Phillipe managed to ask the organisers to get Benny in for free as the official IRC reporter.

Lesson 7: Promote your favourite open source projects. Pay it forward.

Benny's last talk was about Testcontainers pre-pandemic. Later these projects have evolved eventually becoming their own companies.

## Summary

1. Leverage OSS as a learning environment
2. Make your work visible - OSS contributions can be a key differentiator in interviews
3. Get your hands dirty
4. Pay it forward

## Questions

How do you find something that easy to fix as a contribution? For example in Gradle, all the tickets are super hard.

- Most libraries are actually simpler than the code bases you work in commercially.
- The perception of it that they're more complex is often off. Not always.
- The though that you need to understand the whole codebase to contribute isn't always right.
- At least try. That way you might learn something and get better.

- A lot of maintainers have been thinking about this. Often people will provide "great first PRs" or "easy ways to contribute"

What are the best practices for a well run ecosystem for your contributions?

- That's impossible to answer. It heavily depends on the community around it and who is running it.
- Is that project solely run by maintainers working during the night?
- Is it run by a company with people paid to do OSS?
- It's really hard to say there are best practices. Talk to the maintainers and see how they want to run the project and adapt to that and learn about their processes.

In IT there are many jobs other than developers. Data scientists etc. How can they contribute or find things to contribute to?

- This goes back to "what is your goal"? Randomly contribute or have fun with it?
- Go to the projects you use day-in and day-out.
- Nothing makes you happier to use tools that you have contributed to and seeing your fixes in place.
