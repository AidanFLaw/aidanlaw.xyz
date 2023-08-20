---
Title: "Docker (Containers) One of My Favorite Tools"
#image: "/images/WeddingPicture.webp"
#Description: "Getting Married in Poland as an American: A Complex Journey Through Bureaucracy"
Date: 2023-08-08T08:01:00-00:00
Draft: true
Tags: ["Dev", "DevOps"]
Series: "Development"
---
## When I learned about Docker
I first learned about Docker when I started my internship at FWE, in the summer of 2015. My manager at the time saw my eagerness to use Linux and related tools. I was the only person in the company running Linux on the work laptop. He also saw the popularity of containers and by extension docker blowing up on Hacker News, he tasked me with learning how it worked.

At the time we were looking to transition away from our current ticket system to something new. We eventually moved to [Jira](https://www.atlassian.com/software/jira), but before that we experiemented with a few different open source offerings. This was a good way to play with Docker.

At this point Docker Hub was around a year old. This meant there wasn't as robust of an ecosystem for easily deployable images.
I was attempting to depoy [Redmine](https://www.redmine.org/), I did find a couple of dockerfiles floating around Github, for it. I used them as reference but decided to try it myself. After learning how to make images, I went on to make many more.
## Docker Basics
### What is Docker
Without going further, I figure I should explain what Docker is. When I say Docker, I am actually referring to the **Docker Engine**. At its core you have ***dockerd*** this is the actual daemon running the containers. You can interface with it by using the ***docker*** the client CLI program bundled with the engine, or you can acess it via API.

By default **dockerd** listens on a unix socket ```unix:///var/run/docker.sock```. I have seen some pretty cool things done with this, for exampe [Portainer](https://www.portainer.io/) a web UI for managing docker. Can be ran as a child container of the engine it interfaces with. This done by mounting in ***docker.sock*** into the portainer container. 
