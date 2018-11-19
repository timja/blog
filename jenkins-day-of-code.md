# Day of Jenkins as code 
 
On Thursday 18th of October I travelled to Copenhagen for the ”Day of Jenkins as code” conference. 

It was a one day event and a pre-conference meetup on the Thursday evening. 
I attended 6 talks and participated in a hackathon while there. 

Key learnings from the event: 

* the build failure analyzer is a must have plugin when you have lots of developers you are supporting - since the event we have done a successful implementation of it, and the feedback has been great. 
* use the configuration as code plugin for managing your jenkins instance where possible, falling back to groovy init scripts where needed - an example of what we've done: https://github.com/hmcts/cnp-jenkins-config/blob/master/cac-prod.yml, good slides on it here: https://docs.google.com/presentation/d/1_Kx7KAa0YoiRskEtEmFYPFSJq6G576IEGikJFN-ir6w/edit#slide=id.g440eb2b55c_0_3 
* investigate cloud native jenkins as it stabilizes and more of it is released, some examples: https://github.com/Azure/azure-artifact-manager-plugin, https://github.com/jenkinsci/pipeline-log-fluentd-cloudwatch-plugin 

## Day 1 - pre conference meetup 
 
### Developing jenkins pipelines 

Oleg Nenashev, Cloudbees 

https://speakerdeck.com/onenashev/cph-jenkins-meetup-developing-jenkins-pipeline 
 
This was a great talk on pipeline and shared library development, 
two example unit testing libraries for pipelines were mentioned: 
https://github.com/jenkinsci/JenkinsPipelineUnit 
https://github.com/homeaway/jenkins-spock 
Oleg went through how he develops shared libraries, rather than: 

``` 

git commit -m "Jenkins driven development" && git push 

```  

He has written a plugin called [filesystem_scm-plugin](https://github.com/jenkinsci/filesystem_scm-plugin) which uses a directory as the shared library repo, it saves some not having to commit, push and have jenkins pulling and also makes for a much cleaner git history. 

 

To do this it’s much easier if you can run a docker image of Jenkins that is the same as you run in prod, Oleg has an example one here that he uses which can be configured based on environment variables for things like security https://github.com/oleg-nenashev/demo-jenkins-config-as-code 

In general, from talking to lots of people at this event, it seems a lot of people are running their Jenkins masters in docker, something to look at it. 

### Jenkins in public cloud 
Andrey Devyatkin, Praqma 
https://www.slideshare.net/AndreyDevyatkin/running-jenkins-in-a-public-cloud-common-issues-and-some-solutions 

This talk was about running a Jenkins master in a public cloud, some issues and some solutions. 
There was two parts to this talk.

The first part was about the cloud architecture, private / public Jenkins and authentication mechanism in the cloud and the second part was about metrics on Jenkins builds. 

Metrics is a common problem in large Jenkins installations and not well solved currently. 
Andrey is working on an AI based approach which will parse the builds logs and classify errors and provide insights 

Some people are using the build failure analysis plugin which is manual regex based, problems with this approach is it runs line by line through the build log after the build has finished and a bad regex can bring a Jenkins master to its knees. 

Some people are using the Jenkins api for build analysis results but their Jenkins master's tend to get DOSed (denial of serviced) and this approach should be used with caution. 

## Day 2 - the conference 

The day began with breakfast at the conference and some networking, there was people from lots of different industries there, it was really interesting to talk about their work, some examples: hearing aids software, cars and game development 

### 2018 biggest year of Jenkins innovation 
Kohsuke Kawaguchi, founder of Jenkins and CTO of CloudBees 
 
Kohsuke introduced the conference and talked about what this year has brought for Jenkins and what is currently in progress. 
 
The main topics were: 
* Jenkins pipeline 
* Jenkins evergreen 
* configuration as code  
* cloud native Jenkins 
* Jenkins x 

#### Blue ocean 
Working on opening it up for extension and community extension 

It is time to lose name of blue ocean and integrate directly and be a core part of Jenkins UI 
 
#### Configuration as code 

Soo many people have done Jenkins automation in different ways, configuration as code plugin happened from CloudBees and Praqma both working on a solution, found out they were both doing similar things and then they teamed up. 

You write Yaml based configuration and provide an environment variable to use it, most plugins using modern data binding and best practices are supported out of the box, there is an active effort to help plugins that aren't supported get the help they need, [dashboard of known issues](https://issues.jenkins-ci.org/secure/Dashboard.jspa?selectPageId=17346), [plugin developers guide](https://github.com/jenkinsci/configuration-as-code-plugin/blob/master/docs/PLUGINS.md), [demos of compatible plugins](https://github.com/jenkinsci/configuration-as-code-plugin/tree/master/demos). 

There is pretty good documentation on it and if you need help there's a community on [Gitter](https://gitter.im/jenkinsci/configuration-as-code-plugin). 

#### Cloud native Jenkins 

There is a big drive going on to make Jenkins a 1st class cloud native citizen 

What's changing:  
* No more file system storage 
* No more single process, single point of failure 
* Some of the work is well under way, artifact storage (initially with S3 - CloudBees, Azure blob storage being done by Microsoft) and build logs sending elsewhere (first focus is AWS cloud watch) 

Benefits: 
* Big size cut down to jenkins home 
* cheaper storage 
* better scalability 
 
Work being done through cloud native sig (special interest group), there's a mailing list and G itter group: https://gitter.im/jenkinsci/cloud-native-sig 

## Look ma, No hands - Manage jenkins configuration as code 
Nicholas De loof & Ewelina Wilkosz 
https://docs.google.com/presentation/d/e/2PACX-1vRozmQoguBGDegBMZ5MAsG-8LgOXrNOA7Ayi961UAd78ATQz8a6VBQc9T6jO_KLnH6u6-8Xy6PI6cKC/pub?start=false&loop=false&delayms=3000 

2018 is * as code 

infra, env, arch, ci/cd 
 
Configuration as code covers: 
infra, jobs, system config 

Main benefits: 
* Safety 
* Traceability 
* Speed - seconds to apply, and can be done on a running instance vs minutes for a restart 
* Easy to use 
* Easy to reuse 

It works using schema introspection (heavy reflection usage) 

Some features: 

* generates documentation on a running instance showing you where your values need to be in the Yaml file,  
* validation endpoint 
* CLI command for validation / dry run 

## Jenkins evergreen - safely upgrading Jenkins every single day 
Baptiste Mathus, CloudBees 

https://docs.google.com/presentation/d/e/2PACX-1vSMzxYo1JHRweaCTexEymGoMuClscPnTzPtvkkASGMzcLdwoUB5ljBtxrVYcsuasqt11UIEUduKHAIk/pub?start=false&loop=false&delayms=3000&slide=id.p 

https://jenkins.io/projects/evergreen/ 

Evergreen is an automatically updating rolling distribution system for Jenkins. It consists of server-side, and client-side components to support a Chrome-like upgrade experience for Jenkins users. 

A user should be successful with Jenkins in under 5 minutes and 5 clicks 
It will set automatic sane defaults, detect whether running locally, Docker, Azure, Digital Icean, AWS, GCP 

* Automatically updated distribution 
* Automated healthcheck and rollback if need be 
* Anonymous telemetry for warnings and errors 

It's pretty cool, when plugin updates are released they are tested and then manifest is updated and all clients get the new plugin 

##### Best practices 

* Don't run builds on master 
* Run ephemeral agents not pets 
* One approach is to run only 1 build per agent 

Basically you leave it to the evergreen team to maintain your jenkins master version and certain core curated plugins 

Coming soon: 

* auto update evergreen sidecar project 
* metrics telemetry 
* ensure people can go from 0 to building code without roadblocks 
* have number of users > 0 

Currently the evergreen ecosystem appears to be beta ish, it is supposed to be very easy to use, maybe not for everyone, feedback is really really welcomed 

One caveat is that updates restart the master straight away, and this functionality needs more work 

## Jenkins X Makes CI/CD as easy as abcd 

Presented by JFrog - Eyal and Yahav 

https://jenkins.io/projects/jenkins-x/ 

Uses Kubernetes as it’s a common portability platform across clouds 
Distributed highly available Jenkins on Kubernetes  
build engine running as Serverless / function as a service style 
Continuously delivered, with evergreen 
Initially focuses on specific use cases and not for everyone 
 
It comes built in with: 
* Git 
* Jenkins 
* Binary repo manager 
* Docker registry 
* Helm chart repo 
* Kubernetes 

Environments: 
* Preview – ephemeral 
* Staging - updated after every PR merged, always there 
* Production 

A demo was shown with it running on Google Cloud Platform 

I'm not sure if its ready for use yet, but if you can fit into its workflow looks like an easy to setup continuous delivery pipeline. 

Feel free to get in touch if any of this is interesting to you (@Tjaynz on twitter), so far I've tried out most of what is talked about here. 


 
