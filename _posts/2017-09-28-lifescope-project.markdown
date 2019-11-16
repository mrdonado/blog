---
layout: post
title:  "Lifescope Project"
date:   2017-09-27
category: projects
backgrounds:
    - https://blog.jdonado.com/assets/images/backgrounds/lifescope-bg.jpg
    - https://blog.jdonado.com/assets/images/backgrounds/lifescope-bg-2.jpg
    - https://blog.jdonado.com/assets/images/backgrounds/lifescope-bg-3.jpg
thumb: https://blog.jdonado.com/assets/images/lifescope.jpg
tags: python ai nlp nodejs docker architecture reactjs redux
---

# An artificial intelligence software looking for solutions to healthcare problems

## Some history

At some point in March 2017, my friend [Juan Fernández](https://www.linkedin.com/in/juanff/) proposed me to join him in the development of a software he's been working on as a side project. It was a script written in Python that used [spaCy](https://spacy.io/) to analyze messages coming from a twitter stream.

The idea was to use the [Natural Language Processing (NLP)](https://en.wikipedia.org/wiki/Natural_language_processing) engine of [spaCy](https://spacy.io/) to find messages in English where a solution is proposed to a healthcare problem, tagging both problem and solution, and discarding irrelevant messages.

The script also analyzed the author of each message, tagging it automatically (e.g.: doctor, news source, patient...).

He had a first version of the script working on his computer, but he wanted to find a way to show it to the world. So I thought we could turn it into an online web application accessible for everyone and make everything Open Source. Now, the first version of our software is available online at [lifescope.jdonado.com](https://www.lifescope-project.com).

## Architecture

### From local script to the cloud

After some playing around with Flask, I decided that separating the analyzer from the twitter stream and the backend services was the best solution. We wanted a system that **scales** and that is **easily maintainable**. So I came about with the following **microservices** based architecture:

- [A nodeJS service](https://github.com/fjrd84/health-nlp-node), sending messages from the twitter stream to a [beanstalkd](http://kr.github.io/beanstalkd/) jobs queue.
- The [python analysis engine](https://github.com/fjrd84/health-nlp-analysis), which now just takes jobs from beanstalkd and sends the results to Firebase and [Elasticsearch](https://www.elastic.co/products/elasticsearch).
- [Firebase DB](https://firebase.google.com), serving the analyzed messages in real time to the frontend.
- A [ReactJS + Redux](https://github.com/fjrd84/health-nlp-react) based frontend, that works as a showcase for the project, with an introduction, a link to [the project's blog](http://lifescope-insights.jdonado.com/), and a real time timeline of analyzed messages.

![Architecture Diagram](https://blog.jdonado.com/assets/images/lifescope-architecture.png "Architecture Diagram")

## Features

With this architecture, we can show the results of the analysis in **real time**, while they are also saved permanently into the Elasticsearch in order to ease the integration with an **upcoming statistics module**. Firebase takes care of the websockets; so, our services just need to respond to on-demand requests, making scalability less demanding. We clean the Firebase DB from older messages every three hours, so that it loads quickly. Thus, the free plan with 2GB of storage is more than enough for our needs.

The analysis engine is now independent of the source: it just processes jobs that come from beanstalkd with a generic JSON format. We can (and we will) extend the streaming services in order to provide more sources, other than twitter (e.g.: specialized news feeds).

The nodeJS service also has an endpoint to which the frontend can post new messages, so that our visitors can test the analysis engine and see the results right away on the timeline.

## Deployment

Everything is [dockerized](https://www.docker.com/), making the installation and deployment of the whole system a piece of cake, allowing us to easily move our services if the demand grows. We deployed it to a [VPS](https://en.wikipedia.org/wiki/Virtual_private_server) and it's working 24/7, creating an interesting database to analyze using [Big Data](https://en.wikipedia.org/wiki/Big_data) tools.

## And why did you...

- Use [React](https://facebook.github.io/react/) and [Redux](http://redux.js.org/) for such a simple frontend? Side projects are for me a chance to experiment with technologies I'm still no expert with. I started writing it with [Angular](https://github.com/fjrd84/health-nlp-frontend), but since I already had other active projects that used Angular, I decided to rewrite it using React and Redux, because I like to use the time I spend on my side projects to learn new interesting stuff I don't always use on my day job. Maybe just React would've been enough, but anyway, if the frontend grows into something more complex (which is quite likely), its scalability will be easier.
- Use a VPS instead of deploying it to [AWS](https://aws.amazon.com)? A VPS is really cheap, and for this first phase is more than enough for our needs. Also, we're not afraid to maintain ourselves a couple of Linux machines :)


## Further steps

Now it's time to take advantage of the information saved in the Elasticsearch. The next step is to build a new backend service (most likely, [Clojure](https://clojure.org/) based) that provides the frontend with results of a **Big Data** analysis. [D3js](https://d3js.org/) will for certain be a useful tool in order to plot that data on the frontend.

Do you have any questions or remarks? Feel free to ask us anything on the comments section.
