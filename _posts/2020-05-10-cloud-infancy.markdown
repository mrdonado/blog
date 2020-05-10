---
layout: post
title:  "The Infancy of the Cloud"
date:   2020-05-10
category: cloud aws kubernetes cloudfoundry
backgrounds:
    - https://blog.jdonado.com/assets/images/backgrounds/cloud-bg.jpg
thumb: https://blog.jdonado.com/assets/images/cloud.jpg
tags: cloud aws kubernetes cloudfoundry
---

## The Heterogeneous World of IT Job Titles

When I started working in the IT industry, one of the things that shocked me a bit was the way job titles were used. I guess that as an undergrad, you often expect some correlation between training or a study plan and whatever position or role you play in a company. Yes, that was naive from me, I admit it. Then, the creativity some people exercised when describing their job took me by surprise. Whoever has jumped through enough LinkedIn profiles will know what I'm talking about.

I don't want to rant about made-up or senseless occupations here, however. What I want to talk about is a specific position that on the surface makes all the sense in the world, just not to me, not yet.

## The Cloud Expert

The cloud has become an important area for IT professionals. If you don't know anything about the cloud you'll be left behind. Most of you will remember how some years ago, in 2006 Amazon released AWS and the cloud phenomenon started taking off. Some people used to joke that 

> "It was never the cloud, it was always someone else's computer."

And yes, that is more or less what it looked like at the beginning. But then it started evolving. First, the continuous additions to the AWS products landscape, together with Microsoft Azure's, Google's, and IBM's counterparts. Then [Pivotal's CloudFoundry](https://en.wikipedia.org/wiki/Cloud_Foundry) and [AWS CloudFormation](https://aws.amazon.com/blogs/aws/category/management-tools/aws-cloudformation/) in 2011, [Docker containers](https://en.wikipedia.org/wiki/Docker_(software)) in 2013, Google's [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes), [AWS Lambda](https://en.wikipedia.org/wiki/AWS_Lambda) and [HashiCorp's Terraform](https://en.wikipedia.org/wiki/Terraform_(software)) in 2014... just to name a few.

It's 2020 and we **need** cloud experts. However IMHO **nobody out there is a cloud expert**. Not yet. I'm afraid that we didn't have time to digest it all, there's no standardization, no clear best practices and, still worse, it has not stopped evolving. And it doesn't look like it will stop evolving any time soon.

Yes, you might have attended countless conferences as a speaker talking about cloud technologies and you might have world-class expertise about the cloud. Or maybe, you've been using Kubernetes since 2014 and sure, that will make you an expert on that particular technology.

But do you have any certainty about the *near* future of the cloud? Can you do something complex today that will still follow what will be considered best practices in 5 years? I seriously doubt it (just look at how we were doing things five years ago).

## Is It a Bad Thing? What Can We Do?

I think that it's something quite common in IT, as well as in any other branch where things are evolving so fast, and it has pros and cons. On one hand, for us professionals in the field, it's exciting to experience this evolution in first person. On the other hand, it sure creates a lot of noise and fatigue, and you never know which technologies or techniques will stay relevant a couple of years from now.

There is however one thing you can do when starting a new project or when taking architectural decisions on existing projects on the cloud, and it is to **forget for a moment how big of an expert you are**, try to adopt [a beginners mindset](https://www.creativehuddle.co.uk/how-to-adopt-a-beginners-mindset), and follow the golden rule of the software development craft: [the Keep It Simple, Stupid (KISS™) principle](https://en.wikipedia.org/wiki/KISS_principle).

The cloud came to help us with our jobs, not for us to overengineer our solutions or to take the [Resume Driven Development](http://radar.oreilly.com/2014/10/resume-driven-development.html) road. Things tend to get complicated by themselves and it's our responsibility to do our best to keep them as simple as possible.

Some of the clear benefits of the cloud are:

- You don't have to buy or manage hardware yourself
- It *should* be cheaper
- Software updates are managed or simplified
- It offers a fast and reliable service (typically 99.9% availability)
- It makes it easier to write software that scales (although if you're a startup, chances are that [you shouldn't have to worry much about that](http://paulgraham.com/ds.html))

If you misuse the tools that the cloud offers you, however, you can easily ruin its associated benefits, even creating brand new categories of problems. Here are some **common problems** that I've experienced when working on legacy projects on the cloud:

- Using too many resources on the cloud can be *very* expensive.
- It's often the case that you cannot easily set-up a development environment on your local machine because there are too many dependencies on cloud components. You should try to avoid this early on in the project or things will quickly get out of hand, making life hard for you and still worse for newer members of your team.
- Sometimes unit tests depend on existing infrastructure (this is a big red flag).
- No appropriate monitoring and tracing mechanisms have been set up in order to find out exactly what happened when something went wrong.
- It's easy to overcomplicate things when trying to apply architectural patterns like microservices or an event-oriented architecture. Sure, using patterns and tools for the sake of using them has always been an anti-pattern, but the cloud can take it to a whole new level. If there are too many components, it gets more difficult than ever to get a clear picture about what's going on and documentation tends to stay out of date.
- If there's no deployment automation from the beginning (Infrastructure as Code), chances are that you're going to regret it.

## If you *Really* are an Expert, you'll be Humble

As Bret Victor pointed out on his talk about [the Future of Programming](https://www.youtube.com/watch?v=8pTEmbeENF4), the community of IT professionals is (sadly) still digesting ideas that were first formulated during the 60s and 70s.

A good example of that is the renewed interest in Functional Programming and how mainstream Object-Oriented languages like Java, C# and C++ have recently incorporated FP concepts that were already available in LISP, first specified in 1958.

We simply didn't have enough time to define best practices for the cloud yet. Since you cannot know the best practices of the future, try to keep things as simple as possible today, because chances are, you're going to need to rewrite parts of your code in the coming years. The less code you have today, the easier it will be to modify it tomorrow.

Also, your team will be grateful if you do your best to keep documentation up to date. Using tools like Terraform, CDK, or Pulumi we can write Infrastructure as Code (IaC). That serves a double purpose: it lets us deploy and manage our cloud resources automatically, while also providing an up-to-date documentation of your infrastructure. 

What will come next? A tool that generates up-to-date diagrams using our IaC? A WYSIWYG editor for the cloud? At this pace, we'll see it pretty soon.
