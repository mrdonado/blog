---
layout: post
title:  "The Yin and Yang of Software Development: Quality and Context"
date:   2021-06-05
category: software-quality startup product-development
backgrounds:
    - https://blog.jdonado.com/assets/images/backgrounds/buddha.jpg
thumb: https://blog.jdonado.com/assets/images/yin-yang.png
tags: software-quality startup product-development
---

## What Is Software Development All About?

Depending on who you ask, even when asking professionals that have been many years in the industry, you'll get very different answers. In my experience, there are two clear trends:

- **Software Development is a craft**. Your job as a software developer is similar in many ways to the job of a gardener. You don't have a big plan because you don't need one. You're starting small, you're alone and you're building things along the way. If you're a good gardener, however, you'll be clean and you'll pay attention to the tricky parts of your garden because you know that unless you keep them under control, they'll soon get out of hand.

- **Software Development is an Engineering discipline**. Now there's a plan. You have one or even various teams, and you're building something big and complex for which there are clear requirements and constraints. There's a need for a well-defined architecture, as well as processes that ensure the **quality requirements**. [Agile methodologies](https://en.wikipedia.org/wiki/Agile_software_development) might let you delay part of the decision-making, but you cannot sacrifice quality.

You might call it the *Yin and Yang of Software Development*. I think that both statements are true to some extent, but one might rule over the other depending on the context.

There's almost always some craft or even some art in the process of software development. When you're writing code, more often than not you're not following a scientifically rigorous method. Instead of that, you rely on pre-existing components and on your experience to build things that work. If you need metrics, these metrics usually come after you've already built something. Depending on the context, however, you might need to apply engineering processes and quality standards that resemble those used in other engineering disciplines.

These two definitions raise two new questions:

- In which context will one definition rule over the other?
- What is quality in software?

## Business Is the Context

When you build software in the context of a startup that is creating a new product from scratch, there are usually so many unknown factors about the final product that it often makes little sense to apply most software quality measures.

If you follow the principles of [Design Thinking, Lean and Agile](https://www.thoughtworks.com/books/understanding-design-thinking-lean-agile-jonny-schneider), you'll continuously perform experiments and collect data to test your hypotheses. This way, on each iteration you gain more insights about the right direction for your product.

Usually, during the initial iterations, you'll have a pretty small team (maybe just one developer), and the advantages of applying software quality principles might not be too clear because oftentimes the code that you're writing won't live very long.

As Johnny Schneider explains in [Understanding Design Thinking, Lean and Agile](https://www.goodreads.com/en/book/show/35784015):

> [...] Much of this experimentation might not involve writing a line of code—after all, **working software is still an experiment, just a really expensive one**. As confidence increases and software is the experiment, Agile is how teams constantly adapt to change, repeatedly adjusting their course and taking the next steps

Therefore, during the initial phases of a startup you should better spend your time experimenting and collecting data about your experiments to continue taking well-informed decisions. You can interpret this as a technical variant of Paul Graham's ["Do things that don't scale"](http://paulgraham.com/ds.html). What is the advantage of writing a high-quality scalable solution if you end up throwing it all away?

Some developers would be horrified to even think about developing software like that. That's because they don't see software development as a craft nor as an Engineering discipline, *but as a religion*. Good developers see the value in software quality techniques, but they can also see the big picture and the cost of [non-functional requirements](https://en.wikipedia.org/wiki/Non-functional_requirement), especially in the beginning. If you don't see this, you might end up with an overengineered solution, that is difficult to maintain and poorly addresses the business needs.

---

The problem with the context, however, **is that it changes over time**. You might've started it all as a bunch of experiments, but at some point, if enough of these experiments were successful, you'll find yourself offering a real service to real customers.

A good CTO in a startup will constantly try to answer this question: **"how much technical debt can we afford?"**. In the beginning, we need speed over anything else, because we cannot afford to take too long when trying out our experiments. Otherwise, we'll run out of time and resources and our competition will kick us out before we've even started.

[But there's an inflection point](https://blog.devgenius.io/quality-vs-speed-9a46a6254a4e) where the velocity (that is, the development speed of new features) will dramatically decrease if you don't improve the quality of your software. It's difficult to tell when exactly this inflection point occurs for a given product, but some indicators are:

- Your codebase is growing a lot and more people start working on it.
- You start spending more time than usual fixing bugs.
- New developers find it increasingly difficult to start being productive.
- You need an increasing amount of time to manually test your product on each release.
- Your customers start complaining about the quality of the service.

![Development speed vs. time since project start](https://blog.jdonado.com/assets/images/speed-time-chart.png)

The team and its stakeholders should realize that the ludicrous speed of the initial phases is not sustainable over time. At some point, enough resources should be spent to continuously refactor the codebase before it's too late. If the goal is to achieve the highest development speed possible, there's a moment where software quality principles stop being an obstacle and become a crucial part of [a virtuous circle](https://www.youtube.com/watch?v=ZmgbjaRfp_Q) where you build better software, faster. In other words, if you don't care about the quality of your software soon enough, it's a matter of time that it'll become unmaintainable, and that will slow you down.

### Product Development Stages

During product development, we commonly use the following terms:

- [PoC (Proof of Concept)](https://en.wikipedia.org/wiki/Proof_of_concept): it's the minimal solution to see if some idea is even feasible. It's the cheapest way to test a hypothesis.
- [Prototype](https://en.wikipedia.org/wiki/Prototype): it's a piece of working software that solves a specific problem. It might have a beautiful design and it might seem to be robust, but it doesn't follow most software development best practices and quality standards. It's a good way to test a hypothesis, but it's not maintainable in the long term. Some authors use the term "walking skeleton" for this.
- [MVP (Minimum Viable Product)](https://en.wikipedia.org/wiki/Minimum_viable_product): it's a product built on a solid technical base that offers the minimum set of desired features. The goal of an MVP is to hit the market as soon as possible while having a solid starting point that is maintainable in the long term.

After the MVP phase, you might want to continue iterating into a Minimum Marketable Product, a Minimum Lovable Product, a Minimum Delightful Product...

In my personal experience, most problems during product development these days have to do with misunderstandings about where you are with your product from a technical and from a business perspective. It's the responsibility of all the team members to communicate where you are and to understand what it means.

## Quality Software

I won't describe in depth what software quality is all about. [There's an article in Wikipedia](https://en.wikibooks.org/wiki/Introduction_to_Software_Engineering/Quality) that defines it pretty well.

In a nutshell, in the context of a startup, quality software means:

- Follow best practices.
  - From code style guides to DRY, KISS, SOLID principles, etc.
  - Continuously refactor your code.
  - Automatically analyze the complexity of your code.
- Write tests.
  - Not just unit tests, but all kinds of tests. Tests will help you keep your solution stable and maintainable. Read [the practical test pyramid](https://martinfowler.com/articles/practical-test-pyramid.html) for more information about it.
  - Without tests the chances that new features break existing ones without you realizing it are ever-increasing.
  - Tests also work as documentation for new team members.
- Do code reviews.
- Use continuous delivery pipelines.
- Design a scalable architecture.
  - Monitor your systems and identify failures and bottlenecks.

Doing this takes time, but it's an investment. I wouldn't recommend doing it all while the chances are high that you'll end up throwing away most of your code, but as time goes by, you should better spend more and more time doing it if you don't want to end up with a spaghetti code salad that nobody wants to work with.

The later that you start applying these principles, the most difficult (and expensive) it's going to be to apply them successfully.

## Some Final Words

Maybe you've always worked in an environment where quality software is valued and that's why you're probably almost religious when it comes to writing software with high-quality standards. Hopefully, you see now that there are cases where it's in fact better to go the quick & dirty way for a while and activate the feature factory mode.

Feature factory and quick & dirty have a price though, I hope that's a bit clearer now. It's tempting to try to extend that unnatural velocity peak as much as you can, but you're playing with fire here. I've seen again and again companies that followed down that road, only to end up with buggy software and developer scarcity, because nobody wants to feel the pain of working with such a codebase in such conditions. If you're in a team that won't understand that whatever worked yesterday won't work today anymore, I'd suggest avoiding quick & dirty altogether.

Then there's the ghost of a big rewrite. I would avoid rewriting unless it's strictly necessary because it's crazy expensive. If you extend the quick & dirty phase for too long, however, sooner or later there'll be no other choice.
