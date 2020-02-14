# Extending Spring Boot for the Enterprise and Cloud Workshop

Spring Boot has become incredibly popular since it's initial release in 2013. Spring Boot allows developers in the matters of minutes or hours to build functionality that would take days or weeks to build before. 

Over the same time period Cloud Platforms have grown in size and capability. Like Spring Boot Cloud platforms can deliver functionality in minutes or hours what would take many organizations days, weeks, or even months to build out and deploy. 

While this "out of the box" functionality of Spring Boot and Cloud platforms is great, it can be taken further. Spring Boot and Cloud Platforms are built to address very broad domains, but individual organizations will often have a much more narrow focus. In this workshop we will see how we can extend and build on top of Spring Boot and Cloud platforms to better address the needs of our organizations. 

## What You Will Learn
In this workshop you will learn about the following concepts:

* How to better utilize Maven for prject management and governance
* Build custom Spring Boot Starters
* Build automated delivery pipelines
* Deploy and manage applications on a Kubernetes Cluster
* Write reliable automated integration tests
* Connect to and manage cloud services

## Workshop Prerequisites

While this is a full-day workshop, there is a lot of content to cover. For that reason it is HIGHLY recommend that attendees complete this section of the workshop ahead of time:

### All users: 

* Preferred IDE
* [Java 8+](https://adoptopenjdk.net/)
* [Docker](https://www.docker.com/get-started)
* [git](https://git-scm.com/downloads)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [IBM Cloud Account](https://ibm.biz/BdzJmC)
* [IBM Cloud CLI](#configure-ibm-cloud-cli)

### Windows users:

<details>
<summary>Click to Expand</summary>
This workshop makes heavy use of terminal commands. The terminal command examples in this workshop are use *nix idioms. For that reason it is highly encouraged to either use [Cygwin](https://www.cygwin.com/) or [install/enable the Linux Bash shell](https://www.windowscentral.com/how-install-bash-shell-command-line-windows-10) that was added to Windows 10.  
</details>

### Install IBM Cloud CLI

IBM provides the powerful IBM Cloud Command Line Interface (CLI) for interaction with IBM Cloud. In this workshop we will be making heavy use out of the IBM Cloud CLI to carry out commands. We will need to walk through a few steps however to make sure the CLI is configured correctly for this workshop.

1. Download the [IBM Cloud CLI](https://github.com/IBM-Cloud/ibm-cloud-cli-release/releases/)
2. Once the installation process has completed, open a bash friendly terminal window
	
	The IBM Cloud CLI uses a modular design. Functionality for handling many IBM Cloud services is located within these plugins. We will need to install a two plugins for this workshop.
	
3. Install the **Container Registry** plugin:

   ```
   ibmcloud plugin install container-registry
   ```
4. Install the **Container Service** plugin:
	
	```
	ibmcloud plugin install container-service
	```

## 0. Workshop Setup

### Upgrade IBM Cloud

During this workshop we will deploy a live application to IBM Cloud and will be making use of services and tools hosted on IBM Cloud. This workshop also requires an "upgraded" IBM Cloud account. This section will walk you through creating an IBM Cloud account and upgrading it:

1. Once you have completed the sign up process and are signed into your IBM Cloud account on https://cloud.ibm.com expand the **Manage** menu and select **Account**

	![](images/promo-code-1.png)
2. Once on the Account page select **Account settings** on the left hand of the page, and then click the **Apply Code** button: 	

	![](images/promo-code-2.png)
	
3. Use the provided url to enter a promo code for you account. 

5. Login into IBM Cloud CLI with the following:

	```
	ibmcloud login 
	```
   **Note:** If you are using a federated IBM Cloud account [follow these steps](https://cloud.ibm.com/docs/iam?topic=iam-federated_id#federated_id).

5. Run this command to target 'us-south' as the region
	
	```
	ibmcloud target -r us-south -g Default -o <youremailaddress@email.com> -s dev
	```

5. View the current target region with the following:

	```
	ibmcloud target
	```
	You should get output that looks something like this:
	
	```
	API endpoint:      https://cloud.ibm.com   
	Region:            us-south   
	User:              myemail@mail.com   
	Account:           My Account Name   
	Resource group:    Default   
	CF API endpoint:   https://api.ng.bluemix.net (API version: 2.141.0)   
	Org:               myemail@mail.com   
	Space:             dev   
	```
	If the region in your output is `us-south` and `org` and `space` have provided values, move on to [Initializing a Kubernetes Cluster](#initializing-a-kubernetes-cluster). If you are in a different region or `org` and `space` are blank expand one of the below sections.
	
	
	### Fix Target Problems
	<details>
	<summary>Expand here to view setup account instructions</summary>
	
	For easiest following of this workshop, it is best to run everything while targeting `us-south` (Dallas). The free-tier of some services are only avaliable in `us-south` and the insturctuions and console output will also more closely match when using `us-south`. When you initially created your account you might had been in a different region, or some other issue might had occurred. The below steps will walk you through resolving your account issues:
	
	1. Change target region to `us-south`:
	
	```
	ibmcloud target -r us-south
	```
	
	2. IBM Cloud has the "organizations" concept which provides a means for collaboration, logical grouping of applications, and is a security region. From the output of `ibmcloud target` replace the values `YOUR_ORG` and `INITIAL_REGION` with the values of `Org:` and `Region:` respectively.

	```
	ibmcloud account org-replicate YOUR_ORG us-south -r INITIAL_REGION
	```
	
	3. IBM Cloud also has the concept of "spaces" which are sub-group to "organizations", which like "organization" can be used for collaboration, logical group, and act as a security region. We will want to create the `dev` region in our newly replicated `org` with the following command:

	```
	ibmcloud account space-create dev -o YOUR_ORG
	```
	
	4. Finally we will want IBM Cloud CLI to target the org and space we just created as we are going through this workshop this can be done with this command:

	```
	ibmcloud target -o YOUR_ORG -s dev
	```
	
	You should get output that looks like this: 
	
	```
	API endpoint:      https://cloud.ibm.com   
	Region:            us-south   
	User:              myemail@mail.com   
	Account:           My Account Name   
	Resource group:    Default   
	CF API endpoint:   https://api.ng.bluemix.net (API version: 2.141.0)    
	Org:               YOUR_ORG   
	Space:             dev   
	```
	
	If the region is `us-south` and org and space are populated you can move on to [Initializing a Kubernetes Cluster](#initializing-a-kubernetes-cluster). If the region still isn't right or space or org are not populated, verify you run all the above steps or get the attention of someone helping run the workshop.
	</details>
	
### Initialize the Kubernetes Cluster

During the workshop we will be deploying applications to a Kubernetes cluster. We will be using the free-tier cluster available on IBM Cloud to give you an opportunity to get familiar with working with Kuberentes on a cloud platform. Initializing a Kubernetes cluster takes about ~30 minutes, so let's start the process now and let it run in the background as we work through the next steps. To initialize a free cluster run the following command:  

```
ibmcloud ks cluster create --name spring-boot-workshop --zone dal10
```

### Deploy Artifactory

During the workshop we will be building several artifacts. As these projects are built and deployed on a Kubernetes, we will store them within an artifactory instance os they are accessible to build pipelines. 

## 1. Managing Maven Better

Maven is a widely used tool in the Java world for managing a projects dependencies and how it is built. Maven can also be a powerful tool within an organization for providing guidance and governance through the use of parents and BOMs. In the first section of the workshop we will define a parent POM that we will make use of later in this workshop. 

### Dependency Management

### Plugin Management

### Properties

### BOMs

## 2. Getting Started With Starters

Central to Spring Boot's success since it was release are the popular starters. (https://start.spring.io/)[https://start.spring.io/] provides a list for many popular starters. Starters allow developers to quickly get security, data access, messaging, and other capabilities working. How these starters work isn't magic and you can build custom, or extend off existing, starters to address needs within your organization. 

In this section we will create some custom starters addressing the needs of logging, data access, and messaging. 

### Auto-Configuration

Spring Boot is often described as being “opinionated”, one of the ways this is done is through auto-configuration. Spring components can be automatically loaded into context without requiring developer intervention. This is great for making your starter easier to consume and also used correctly (i.e. ensure required classes/beans are loaded).


### Conditionals

### Meta-Configuration 

Logging

Data

Observability

## 3. Reliable and Portable Integration Testing

In the next step we will be deploying our applications to the cloud. To do this we will be define continuous delivery pipelines to handle the building and deployment of our applications to the kubernetes cluster. The foundation on which continuous delivery is built is with a reliable and dependable automated test suite. Integration test are a notoriously difficult area to write tests that are both dependable and portable. Tests that depend upon a remote service in either a development or production region are subject to state changes in those services, those service being unavailable, or a change in configuration. This can lead to false negatives. 

In this section of the workshop we will look at using a couple of tools; Test Containers and Spring Cloud Contract for building integration tests that are reliable, portable, and give organizations confidence in their automated test quites allowing them to pursue practices like Continuous Delivery. 

### Test Containers

Test containers is a library built that plugins into the popular JUnit library that allows for the construction and tearing down of a Docker container within the lifecycle of an automated test. 

### Spring Cloud Contract

## 4. Automating Deployment to the Cloud

Deploying an application to production often requires a number of steps that must be executed precisely for a deployment to be successful. Performing these steps manually is time consuming an error prone. Automating the steps to deploy an application to production not only makes the process much faster, but also more reliable. We will be setting up some very simple build pipelines in this workshop, but IBM Cloud and other cloud platforms allow additionally services to be added to check for vulnerbailities, perform static quality analysis, monitor builds for issues, and perform other checks to make sure code being pushed to production successfully and is of high quality. 

## 5. Connecting to Cloud Services

Cloud platforms provide a catalog of pre-defined services for addressing many of the common and not so common needs of enterprises. Services like database persistence, logging, observability, and more. For many organizations beyond being reliable, persistence and retrival isn't a differentiator. In these cases it can make sense for an organization to off-load the work of maintaining a database to a cloud provider, to better focus on areas of core business concerns. In this section of the workshop we will connect our applications and Kubernetes cluster to some of the service IBM Cloud provides. In the span of a few minutes we will have database access, logging aggregation, and metrics gathering capabilities. Capabilities that would often take weeks or months to configure and setup for many organizations. 

## 6. Zero Downtime Deployments

With capabilities like Continuous Delivery, delivery of new code to production is fast and reliable, often starting with a simple code commit. This allows organizations to push new code to production frequently, possibly even multiple times a day. While this is great, if we don't handle rollouts of new version of code to production gracefully, this can result in service disruptions for clients using our applications. 

This can be avoided however with the usage of liveliness and readiness probes in Kubernetes. Kubernetes uses these probes to determines when a pod is in a state to ready to receive traffic. 

## 7. Observability

Understanding the current state, behavior, and health of your applications is key to ensuring clients using your applications are haivng a positive experience, but also for you organization on how to improve your appplication going forward. 

Spring Boot and cloud platforms provide a lot of options for monitoring applications. In this section we will look at how we can enable and build off those capabilities so that we can have a clear understanding of how our applications are behaving in production and how to improve them going forward. 

## 8. Creating a CQRS Architecture

Command query responsibility segregation (CQRS) is a architecture pardgim wherein the responsibilities of reading from a datastore and writing to that datastore are separated in discrete services. There can be a number of reasons to implement this architectural pattern:

* Reducing complexity within individual services
* Security 
* Performance

In this section we will implement CQRS in our architecture through the usage of a kafka message service. 

## Extra Credit

-> use personal docker repository
-> use github repos
-> performance
