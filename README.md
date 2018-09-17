# Using Sitecore and Docker, Overview

Resources for using Sitecore and Docker, because buzzwords hurt. This is a layman's guide to navigating ever changing marketing terms. This is intended to be agnostic to Azure, AWS or a server sitting under someone's desk. The audience for this is for beginners, more technical details will be added later for advanced users familiar with Docker.

## What?

After Docker, Visual Studio/MSBuild is installed on a Windows machine, you should only have to do the following:

1. `git pull origin master`
2. `docker-compose up`

This will install and run Docker images. Docker images are nothing more than a virtual machine, with a scripting file called `DOCKERFILE`. Because Sitecore needs SQL, Solr, IIS and all as separate machines, we create a file `docker-compose.yml` that takes all the `DOCKERFILE` virtual machines and puts them into a little network.

*All* needed to run a complex architecture like Sitecore is simply `docker-compose up`. The machines are accessible via `http://localhost:portforspecificVM`. This can get a lot more complex. When `docker-compose down` is run the machine is gone. Logs, any custom code, everything. There can be exceptions to this too, but in general everything should be wiped out. In production, possibly other environments, persisting things like the database maybe necessary. Currently, we are using Unicorn to migrate serialized files down ontop of a base Sitecore 9 instance. If we redeploy a new build, everything gets wiped out and we start over. This is what we want, and there are many advantages to this.

## Why is a special Sitecore wiki for this necessary?

Sitecore does not provide docker files and as a propietary platform there are some hoops needed to create a Sitecore instance with everything running, namely Sitecore itself and a license file. At the end of all this, your environment should have what is known as a Docker repository for Sitecore. This is private, and allows you to more or less issue the command of `give me Sitecore 9 and all the stuff that goes with it`. This is confusing as the documentation instructs how to build Sitecore from scratch. Download here, place license file here. For beginners, it makes it seem complicated to get going as it assumes an advanced understanding of Docker and Sitecore itself. This should in reality be rare that you'd need to build up the base image after creation, an average developer is just pushing code to an existing installation as ever before.

## How does it work on my local development environment?

On local machines there is a "watch folder" that we publish to. Think of this as whatever is in inetpub sans the Sitecore base files. Those are already on the base Sitecore 9 docker image. We are just publishing in Visual Studio ontop of that. Docker watches for file changes and does a magical sync. You do nothing.

## What about other environments?

For simplicity, we have environments for Continuous Integration, QA and Production. For Continuous Integration, which gets triggered on every merge to the `development` branch, we have it set up as a watch folder to reduce speed in seeing builds created. This generally works and allows everyone to see changes as fast as possible. Building, pushing to a Docker repository (different from git!), moving the image onto CI takes time and the benefits are usually minimal. Arguably this goes against everything Docker is about, but we are creating an environment from scratch each time, just not going through the entire pipeline. Base Sitecore 9, MSBuild and Unicorn sync on that.

## And QA/Production/etc.?

This triggers a build of an image. A build server, or cloud equivalent, will trigger a build on git commit on whatever branch it is hooked to trigger on. It will get tagged and committed to the Docker repository. On noticing that there is something new in the Docker repository, the build server will then deploy the image deployment. How this is done is different depending on if you're using Azure or AWS. Azure has the best support for this as it is more or less automatic. AWS or on-premise will need some minor additional scripting. The important thing is that the image is *exactly* the same as it is on QA. To the point where a true rollback would occur if needed.

## If the image is the same what about configs?

There's two theories on this:

1. Multi-step build process to include config transforms. Each environment would, technically, be a different image. I could not copy and paste QA to Production as the configs are different.
2. Use .NET 4.7.1 or higher and ConfigurationBuilder to inject config values via Environment Variables. This is the more Docker and Linux way of configuration management. Secrets are stored themselves in a separate git repository, but only for posterity. Spinning up new environments is not usual.

We take the second approach as it also encourages the good Sitecore practice of trying to not touch configs as much as possible between environments. Ideally, swapping out values is all that is needed.

## What is Kubernetes?

Kubernetes is one of the many ways to manage clusters. Azure supports this by default, doing it yourself is a pain in Windows but definitely possible. It monitors the Docker images, updates them as necessary. If an image goes down or needs to be scaled up Kubernetes does this. This is not needed for a develoopment machine, but definitely for higher environments.

Helm is a tool to manage Kubernetes, which makes it even more abstract. Most organizations will not benefit from Helm unless the project is large. I would stay away from Kubernetes and other higher level management tools until a base Docker environment is setup.

## What other advantrages are there to Docker?

Great question! There are many besides simply have consistency between environments. If setup correctly, it is trivial to setup new environments, so much so that creating new environments *for each feature branch* may be automated. Consistency and ease of deployment are the largest advantages to date, however. No more 5,000 line scripted installs like SIF. Rollbacks are really rollbacks, it is the exact same image.

## What are the disadvantages to Docker?

Windows support is mature, but still lacking. There are not so insignificant performance problems. AWS doesn't support Windows containers, but you can spin up your EC2 instance with containers. A lot of advantages of Docker and related tools are lost in this however. Scaling and recovery are limited. If this were a native Linux cluster, it would simply be saying checking a button saying scale as needed. Sitecore has other inherent limitations that scaling horizontally (adding IIS nodes) has licensing ramifications. As of this writing newer licenses may not have this limitation. Contact Sitecore on your license restrictions.

## Additional thoughts

* We use Solr on a smaller Linux VM instead of spinning up Windows.
* We ignore having a SQL instance and use LocalDb, then swap out the connection strings in higher environments. Hopefully this will be a managed SQL instance. We build up the Sitecore 9 instance with WFFM and Powershell, create an MDF backup and put this on the base image. Unicorn is layered ontop of that except for production.
* We use SIF to create the base image, but for simplicity sake I left that out. This can be optimized.
* Things are not the exact same on global, multi-region instances. However, the difference is the number of nodes which is simply pressing a button in your cloud provider. The images themselves are the same.
* Don't go down the rabbit hole of all that can be done. Get something working and add things as needed.
* General development good practice but mocking out third party dependencies makes at least local development quicker.

### TODO: Add Links
