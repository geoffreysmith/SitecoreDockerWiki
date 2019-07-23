# Sitecore for Docker Introduction

--
## Overview
### Goal of this Guide
At the end of this guide we should:

* Installed Docker for Windows
* Understand the basic usage of Docker
* Create a Sitecore image for a project called Habitat-Docker based on Sitecore XP1 Standalone 9.2 (Developer Edition)
* Go in-depth into create a full production devops workflow, though the basic concepts here should transfer over
* See the sample Sitecore site from a clean install by simply running the commany `docker run`

> N.B. For simplicity this guide is *opinionated.* For example, things like environment variables in configs, "http://habitat.local" may be part of the Docker build pipeline or the MSBuild/Visual Studio build pipeline. The guide will attempt to call out where this is not something Docker related, these are preferences that are part of any CI pipeline used today.


> The term docker repository should not be confused with a git repository and this guide will do its best to distinguish between the two. A docker repository is where you'd store your docker images if you needed to share them with a team. Much like git, docker repositories can and do exist locally. We will be using a local docker repository.


### Why the complexity?
Docker is simply a VM in the end, but greatly speeds up development and creates predictable builds for both developer environments and higher environments. Docker can seem overwhelming but it helps to look at the typical use case for Docker. Lets say I want to setup Redis quickly. Instead of doing a full Redis install with dependencies, then figuring out what on my local machine doesn't work, we run:

`docker pull redis`
`docker run redis`

But Sitecore is propietary and to make it as easy as the above the following which are not publically available:

* Sitecore installation zip file
* License.xml file

This really only needs to be done once, for each environment per version.

### Sample Habitat-Docker Readme

This is what we will want the simple steps in our Habitat-Docker Readme to look like:

* `docker pull habitatdocker/developer:latest` 
* `docker start developer`
* `git pull https://github.com/mycompany/habitat.git`
* `cd .\habitat`
* `dotnet publish .\habitat`
* Verify `http://habitatdocker.local` runs in browser

## Step by Step Guide

TBD
