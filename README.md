# Extending Spring Boot for the Enterprise and Cloud Workshop

Spring Boot has become incredibly popular since it was first released in 2013. Starting from scratch; a developer can have an application communicating with a database through RESTful endpoints that have security configured for them all within the span of an hour. This was a huge improvement over the often days it would take to accomplish similar tasks using just the core Spring Framework.

Similarly Cloud platforms are also allowing organizations to rapidly build and develop new projects. Whereas before it would often take weeks or months to provision server space, with cloud platforms this can be done near instantaneously. 

In this workshop we will look at how to extend Spring Boot to better meet the needs of our enterprise and take those applications into the cloud using Kubernetes! 

## Pre-Requisties

This workshop requires attendees to have several tools installed on their computer. It is HIGHLY recommended you have these tools installed before you come to the workshop as internet speeds might be lower during the workshop.

* An IDE of your preference
* Java 8 or later
* Git
* Docker
* IBM Cloud account
* IBM Cloud CLI
* Kubectl

## 0. Workshop Setup

Copy from other workshops

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

## 8. Creating a CQRS Architecture


## Extra Credit

-> use personal docker repository
-> use github repos
-> performance
