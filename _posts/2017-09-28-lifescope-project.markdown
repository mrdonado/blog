---
layout: post
title:  "www.lifescope-project.com"
date:   2017-09-27
category: projects
backgrounds:
    - /assets/images/backgrounds/lifescope-bg.jpg
    - /assets/images/backgrounds/lifescope-bg-2.jpg
    - /assets/images/backgrounds/lifescope-bg-3.jpg
   # - https://blog.jdonado.com/assets/images/lifescope.jpg
thumb: https://blog.jdonado.com/assets/images/lifescope.jpg
tags: python ai nlp nodejs docker architecture reactjs redux
---

# Artificial intelligence software looking for solutions to healthcare problems

## Some history

At some point in March 2017, my friend [Juan Fernández](https://www.linkedin.com/in/juanff/) proposed me to join him in the development of a software he's been working on. It was a Python script, that used [spaCy](https://spacy.io/) to analyze messages coming from a twitter stream.

The idea was to use the [Natural Language Processing (NLP)](https://en.wikipedia.org/wiki/Natural_language_processing) engine of [spaCy](https://spacy.io/) to find messages in English where a solution is proposed to a healthcare problem, tagging both problem and solution and discarding irrelevant messages.

He had a first version of the script working on his computer, but he wanted to find a way to show it to the world. So I thought we could turn it into an online web application accessible for everyone and make everything Open Source. Now, the first version of our software is online at [www.lifescope-project.com](https://www.lifescope-project.com).

## From local script to the cloud

After some playing around with Flask, I decided decided that separating the analyzer from the twitter stream and the backend services was the best solution. We wanted a system that scales and that is easily maintainable. So I came about with the following microservices based architecture:

- [NodeJS service](https://github.com/fjrd84/health-nlp-node), sending messages from the twitter stream to a beanstalkd jobs queue.
- The [python analyzer engine](https://github.com/fjrd84/health-nlp-analysis), which now just takes jobs from beanstalkd and sends the results to Firebase and Elastic Search.
- Firebase, serving the analyzed messages in real time to the frontend.
- A [ReactJS + Redux](https://github.com/fjrd84/health-nlp-react) based frontend, that works as a showcase for the project, with an introduction and a real time timeline.

With this architecture, we can show the results of the analysis in real time, while they are also saved into the Elastic Search in order to ease the integration with a new statistics module (still in development). Firebase takes care of the websockets, so, our services just need to respond to on-demand requests, making scalability less demanding.

The analysis engine is now independent of the source: it just processes jobs that come from beanstalkd with a specific JSON format. We can (and we will) extend the streaming services in order to provide more sources, other than twitter (e.g.: specialized news feeds).

The NodeJS service has also an endpoint to receive jobs from the frontend, so that our visitors can send example messages to the analysis engine, and see the results right away on the timeline.

Everything is [dockerized](https://www.docker.com/), making the installation and deployment of the whole system a piece of cake, allowing us to easily move our services if the demand grows.