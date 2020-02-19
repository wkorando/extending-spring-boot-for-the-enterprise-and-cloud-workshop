# Extending Spring Boot for the Enterprise and Cloud Workshop

Spring Boot has become incredibly popular since it's initial release in 2013. Spring Boot allows developers in the matters of minutes or hours to build functionality that would take days or weeks to build before. 

Over roughly the same time period public Cloud Platforms have grown in size and capability. Like Spring Boot, Cloud platforms can deliver functionality in minutes or hours what would take many organizations days, weeks, or even months to build out and deploy. 

While this "out of the box" functionality of Spring Boot and Cloud Platforms is great, it can be taken further. Spring Boot and Cloud Platforms are built to address very broad domains, but individual organizations will often have a much more narrow focus. In this workshop we will see how we can extend and build on top of Spring Boot and Cloud platforms to better address the needs of our organizations. 

## What We Will Cover
In this workshop you will cover the following concepts:

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

## Workshop Tips

### Workshop Code

All the code that will be used in this work shop is located in this github repository: https://github.com/wkorando/cloud-native-for-spring-developers

Cloning the above repo will bring down all the child projects within it. 

### Finished Code Examples

Finished, or as near as can be, examples of projects are available within the "finished" folder of the project. If you are running into a weird issue and/or something just refuses to work, be sure to check out or copy over the code from the finished folder. 

### Keep a Text Editor Open

There are a few values you will need through out the course, it would be good when first looking them up to copy them to an open text file for later reference instead of having to re-look them up every time. 

### Go at Your Own Pace

Workshops are for getting hands on experience with a technology. The initial presentation, which can be found here, is for providing context for why should consider extending spring boot or using cloud platforms, but ultimately 

### Don't be Afraid to Ask Questions!

If you are running into an issue or just have general "why?" question about the workshop, don't be afraid to ask! The purpose of the workshop is to learn how to use a new technology, so take the opportunity!

## 0. Workshop Setup

### Upgrade IBM Cloud

During this workshop we will deploy a live application to IBM Cloud and will be making use of services and tools hosted on IBM Cloud. This workshop also requires an "upgraded" IBM Cloud account. This section will walk you through creating an IBM Cloud account and upgrading it:

1. Once you have completed the sign up process and are signed into your IBM Cloud account on https://cloud.ibm.com expand the **Manage** menu and select **Account**

	![](images/promo-code-1.png)
2. Once on the Account page select **Account settings** on the left hand of the page, and then click the **Apply Code** button: 	

	![](images/promo-code-2.png)
	
3. Use one of the promo codes in this [spreadsheet](https://docs.google.com/spreadsheets/d/1idaXZt38ehGcJ2LHP4gjQONwwSK6Cyiwd7UYqkcCa24/edit?usp=sharing) to upgrade your account. 

### Initialize the Kubernetes Cluster

During the workshop we will be deploying applications to a Kubernetes cluster. We will be using the free-tier cluster available on IBM Cloud to give you an opportunity to get familiar with working with Kuberentes as well as see the applications we are building during this workshop in action. Initializing a Kubernetes cluster takes about ~30 minutes, so let's start the process now and let it run in the background as we work through the next steps. 

1. From the main page of [IBM Cloud](https://cloud.ibm.com) in the top middle of the page search for "Kubernetes" and select "Kubernetes Service" in the drop down

	![](./images/k8s-step1.png)
	
2. Click the "Create" button in the top right of the page
	
	![](./images/k8s-step2.png)

3. Select the "Free" option

	![](./images/k8s-step3.png)

4. Click the "Create" button on the bottom righr of the page

	![](./images/k8s-step4.png)

### Configure IBM Cloud CLI

1. Login into IBM Cloud CLI with the following:

	```
	ibmcloud login 
	```
   **Note:** If you are using a federated IBM Cloud account [follow these steps](https://cloud.ibm.com/docs/iam?topic=iam-federated_id#federated_id).

2. Run this command to target 'us-south' as the region
	
	```
	ibmcloud target -r us-south -g Default -o <youremailaddress@email.com> -s dev
	```

3. View the current target region with the following:

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
	<summary>Expand here to view instructions if you are having trouble with targeting an IBM Cloud Region</summary>
	
	For easiest following of this workshop, it is best to run everything while targeting `us-south` (Dallas). The free-tier of some services are only avaliable in `us-south` and the insturctuions and console output will also more closely match when using `us-south`. When you initially created your account you might had been in a different region, or some other issue might had occurred. The below steps will walk you through resolving your account issues:
	
	1. Change target region to `us-south`:
	
	```
	ibmcloud target -r us-south
	```
	
	2. IBM Cloud has the "organizations" concept which provides a means for collaboration, logical grouping of applications, and is a security region. From the output of `ibmcloud target` replace the values `YOUR_ORG` (this would be the email you used to create your IBM Cloud account with) and `INITIAL_REGION` (the region you were in when you originally created your IBM Cloud account, you can expand the list below) with the values of `Org:` and `Region:` respectively.
	

	```
	ibmcloud account org-replicate YOUR_ORG us-south -r INITIAL_REGION
	```
	
	<details>
	<summary>IBM Cloud Regions</summary>
	
		Name       Display name   
		au-syd     Sydney   
		jp-tok     Tokyo   
		eu-de      Frankfurt   
		eu-gb      London   
		us-south   Dallas   
		us-east    Washington DC 
		
	</details>
	
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
	
### Setup GitLab Access

IBM Cloud hosts a GitLab instance. During this workshop we will be making updates and changes to code projects that will be hosted on this GitLab instance. To pull and push changes to projects stored here we will need to run through a couple of configuration steps below.

1. Access your GitLab profile through this link: https://us-south.git.cloud.ibm.com/
2. To the top right of the page click o nthe avatar drop down and select settings
	![](images/access_token1.png)
3. 	On the left hand side of the page select access tokens
	![](images/access_token2.png)
4. 	Provide a name for the access token, the name doesn't matter, select a date in the future, check all the boxes to make sure you have full read/write access to your git repositories, and click "Create personal access token"
	![](images/access_token3.png)
5. 	After the page refreshes at the top of the page is the newly created personal access token. As the text below it states, make sure to copy this token value down as you cannot access it later (you will have to repeat these steps)
	![](images/access_token4.png)

### Configure Kubectl

[kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) is a command line tool for interacting with a Kubernetes cluster. kubectl allows you to deploy applications to your kuberneters cluster, view the status of applications on your kubernetes cluster, and perform maintenance work.

From the terminal you are logged into IBM Cloud CLI on run the following commands to configure kubectl to point to the Kubernetes cluster we are spinning up. 

1. Run the following command:
	
	```
	ibmcloud ks cluster config  mycluster
	```
	---
	**Tip:** If you receive an error it might be because your kubernetes cluster isn't finished setting up yet, trying waiting a couple of minutes and running the command again.
	
	---
	
2. In the out for the response you should see something like this:

	```
	export KUBECONFIG=/HOMEFILEPATH/.bluemix/plugins/container-service/clusters/mycluster/kube-config-hou02-mycluster.yml
	```

	Copy and paste that content into your terminal and execute it
	
3. Execute the following command to make sure you are connected to your Kubernetes cluster:

	```
	kubectl get nodes
	```
	
	You should get back output that looks like this:
	
	```
	NAME           STATUS   ROLES    AGE   VERSION
	10.76.68.155   Ready    <none>   8d    v1.15.8+IKS
	```
	
### Deploy Artifactory

During the course of this workshop we will be building several artifacts; Maven POMs and shared libraries, that will be consumed by other projects. We will also be using IBM Cloud to handle the building and deployment of the applications we will be writing in this workshop. So that the shared artifacts are available as they are being built by IBM Cloud we will deploy an instance of Artifactory that will be running on our Kubernetes cluster.

1. Right click on the below button to open in the window in a new tab

	<span style="font-size: 18pt">Deploy Artifactory: </span>[![Deploy to IBM Cloud](https://cloud.ibm.com/devops/setup/deploy/button.png)](https://cloud.ibm.com/devops/setup/deploy?repository=https://github.com/wkorando/cnsd-artifactory&branch=master&env_id=ibm:yp:us-south)

2. For the Lite account users, make sure the region (1) is set to Dallas (us-south). Next, click (2) to configure the Delivery Pipeline.

	![](./images/pipeline-1.png)

3. Next create an API Key for this repo by clicking **Delivery Pipeline** and then the **Create** button. The default values that are generated should be fine.

	![](images/create_api_key.png)

4. Cick the **Create** button in the top right corner of the page.

---
**Tip:** We will be setting up several pipelines, so you we will referencing these steps several times during the workshop.

---

#### Lookup Artifactory Connection Information and Configuration

To be able to publish artifacts to artifactory and pull artifacts from it, we will need to know where it resides on the kubernetes cluster. 

1. Run the following command to get the IP address of your kubernetes cluster
 
```
ibmcloud ks workers --cluster mycluster
```

You will get output that looks like this, you will want the value listed under "Public IP":

```
OK
ID                                                     Public IP     Private IP     Flavor   State    Status   Zone    Version   
kube-bp0ov84d0c6cn0gekpjg-mycluster-default-0000002e   50.23.***.***   10.76.68.155   free     normal   Ready    hou02   1.15.8_1530*   
```
2. Run the following command to get the port number artifactory resides on:

```
kubectl get services
```
You will get output that looks like this, you will want the port that is in the 30000 range. 

```
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
artifactory-port   NodePort    172.21.205.227   <none>        8081:30414/TCP   11h
...
```

3. Copy the url and port (e.g. `50.23.***.***:30414`) into your browser and you should come to your artifactory page
4. You can skip through the first two setup steps, however on the third step when they ask for what type of repo to setup up, select "Maven"
5. From the main artifactory page to the top right click the "Welcome, admin" drop down and select "Edit Profile"

	---
	**Tip:** If you aren't logged in yet, the default username/pass for artifactory is `admin`/`password`
	
	---
	![](./images/artifactory-config1.png)
	
6. Unlock the page by entering the account password (default `password`) and copy the "encrypted password" field to your scratch pad

	![](./images/artifactory-config2.png)

## 1. Managing Maven Better

Maven is a widely used tool in the Java world for managing a projects dependencies and how it is built. No doubt many attending this workshop have extensive experience using Maven! 

Maven can be a powerful tool within an organization for providing guidance and governance for projects. Maven can also be a frequent source of frustration if parent POMs or shared library POMs are defined properly. In the first section of the workshop we will define a parent POM that we will make use of later in this workshop and use it as an opportunity to learn about some features and practices within Maven that can make Maven easier to work within an enterprise.

1. Let's get started on how to better define Maven parent POMs by setting up a build pipeline. 

	<span style="font-size: 18pt">Setup Maven Parent Pipeline: </span>[![Deploy to IBM Cloud](https://cloud.ibm.com/devops/setup/deploy/button.png)](https://cloud.ibm.com/devops/setup/deploy?repository=https://github.com/wkorando/cnsd-produce-parent&branch=master&env_id=ibm:yp:us-south)
	
2. Follow the steps from deploying artifactory above to complete the process of setting up the pipeline. 
3. From the main toolchain page click on the "Git" card

	![](./images/git-clone1.png)
4. On the top right side of the page click on "clone" and copy the https field

	![](./images/git-clone2.png)
5. Run git clone to clone the repository to your local
	
	```
	git clone https://us-south.git.cloud.ibm.com/YOUR_USERNAME/produce-parent.git
	```

### Configuring Deployment to Artifactory

We will need to do a few steps so that when the pipeline builds are projects, they deploy to the artifactory instance we setup during the workshop setup section. 

1. From the pipeline page 
2. 

### Dependency Management

Organizations often have a preferred list of libraries for handling the functional requirements of a project. Having preferred libraries allows an organization to vet them for security concerns as well as become familiar with their operation. However not ever project is going to need every library, so concretely defining them in a parent POM can lead to child projects becoming very bloated. Luckily Maven provides a nice middle with dependency management that allows a POM to define how a library should be consumed: version, scope, etc., but not require it usage by child projects. Let's define some libraries with dependency management:

```
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.my.developer</groupId>
				<artifactId>starter-logging</artifactId>
				<version>${my-org.version}</version>
			</dependency>
			<dependency>
				<groupId>org.my.developer</groupId>
				<artifactId>starter-data</artifactId>
				<version>${my-org.version}</version>
			</dependency>
			<dependency>
				<groupId>org.my.developer</groupId>
				<artifactId>starter-messaging</artifactId>
				<version>${my-org.version}</version>
			</dependency>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-starter-sleuth</artifactId>
			</dependency>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>
```

### Plugin Management

Plugin management works similarly to dependency management. A POM can define the configuration of a plugin without forcing a child project to use it. Like dependencies, there are some plugins that not every project will need, so defining the configuration of these plugins in a plugin management block can be very helpful. 

```
	<build>
		<pluginManagement>
			<plugins>
				<plugin>
					<groupId>org.springframework.cloud</groupId>
					<artifactId>spring-cloud-contract-maven-plugin</artifactId>
					<version>${spring-cloud-contract.version}</version>
					<extensions>true</extensions>
					<configuration>
						<baseClassForTests>${contracts.base-class}</baseClassForTests>
						<contractsRepositoryUrl>${contracts.repo}</contractsRepositoryUrl>
						<contractDependency>
							<groupId>${project.groupId}</groupId>
							<artifactId>${project.artifactId}</artifactId>
							<version>${project.version}</version>
						</contractDependency>
						<contractsMode>LOCAL</contractsMode>
					</configuration>
				</plugin>
				<plugin>
					<groupId>org.asciidoctor</groupId>
					<artifactId>asciidoctor-maven-plugin</artifactId>
					<version>1.5.3</version>
					<executions>
						<execution>
							<id>generate-docs</id>
							<phase>prepare-package</phase>
							<goals>
								<goal>process-asciidoc</goal>
							</goals>
							<configuration>
								<backend>html</backend>
								<doctype>book</doctype>
							</configuration>
						</execution>
					</executions>
					<dependencies>
						<dependency>
							<groupId>org.springframework.restdocs</groupId>
							<artifactId>spring-restdocs-asciidoctor</artifactId>
							<version>${spring-restdocs.version}</version>
						</dependency>
					</dependencies>
				</plugin>
				<plugin>
					<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-jar-plugin</artifactId>
					<version>3.1.2</version>
					<configuration>
						<archive>
							<addMavenDescriptor>false</addMavenDescriptor>
							<manifest>
								<addDefaultImplementationEntries>false</addDefaultImplementationEntries>
								<addDefaultSpecificationEntries>false</addDefaultSpecificationEntries>
							</manifest>
						</archive>
					</configuration>
				</plugin>
			</plugins>
		</pluginManagement>
	</build>
```

### Properties

Maven POM files have a section for defining properties. These properties can then be referenced in other sections of the file. Using properties to define configurable values in dependnecies or plugin, can give downstream projects additional flexibility when working with dependencies and plugins. 

```
	<properties>
		<my-org.version>0.0.1-SNAPSHOT</my-org.version>
		<contracts.repo>git://path-to-your-contracts-repo.git</contracts.repo>
		<contracts.base-class>fully.qualified.path.to.BaseClass</contracts.base-class>
		<java.version>1.8</java.version>
		<spring-cloud.version>Greenwich.SR1</spring-cloud.version>
		<spring-cloud-contract.version>2.1.1.RELEASE</spring-cloud-contract.version>
	</properties>
```


## 2. Getting Started With Starters

Central to Spring Boot's success are the popular starters. [https://start.spring.io/](https://start.spring.io/) provides a list for many popular starters. Starters allow developers to quickly get security, data access, messaging, and other capabilities working. How these starters work isn't magic and you can build custom, or extend off existing, starters to address needs within your organization. 

In this section we will create three custom starters that we will use later when building applications in this workshop. These starters will cover observability, data access, and messaging. 

<span style="font-size: 18pt">Setup Logging Starter Pipeline: </span>[![Deploy to IBM Cloud](https://cloud.ibm.com/devops/setup/deploy/button.png)](https://cloud.ibm.com/devops/setup/deploy?repository=https://github.com/wkorando/cnsd-starter-logging&branch=master&env_id=ibm:yp:us-south)


### Optional Dependnecies

```
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
			<version>${spring.boot.version}</version>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-configuration-processor</artifactId>
			<version>${spring.boot.version}</version>
			<optional>true</optional>
		</dependency>
```

### Auto-Configuration

Spring Boot is often described as being “opinionated”, one of the ways this is done is through auto-configuration. Spring components can be automatically loaded into context without requiring developer intervention. This is great for making your starter easier to consume and also used correctly (i.e. ensure required classes/beans are loaded).

![](./images/autoconfig.png)

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.my.developer.logging.ControllerLoggingAspect
```


### Conditionals

Some beans will only need to be used if certain conditions are met. The Spring team provides a lot of flexibility when defining conditionals. Conditionals can be actively activated through the usage of properties, or passively activated when a class is discovered on classpath or a Bean is present in the application context. 

```
@ConditionalOnProperty(value = "my-org.logging.endpoints")
```

### Meta-Configuration 

Inevitably some libraries will require configuration. Remembering the exact name for a property or the correct values to supply it with can be difficult. To address this the Spring team provides a mechanism for defining metadata about configuration properties which can provide developers with which properties are available, information about those properties, and hints on valid values. This is a great way to provide documentation at the point where it is being consumed.

![](./images/metadata.png)

```
{
	"properties": [
		{
			"name": "my-org.logging.endpoints",
			"type": "java.lang.Boolean",
			"description": "Set this value to false to disable logging of REST endpoints",
			"defaultValue" : "true"
		}
	]
}

```

### Would You Like to Know More?

More in-depth information on creating Spring Boot starters can be found here: https://billykorando.com/2019/12/30/building-a-custom-spring-boot-starter/

As well as on the official Spring Boot user guide: https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-developing-auto-configuration

## 3. Automating Deployment to the Cloud

Deploying an application to production often requires a number of steps that must be executed precisely for a deployment to be successful. Performing these steps manually is time consuming and error prone. Automating the steps to deploy an application to production not only makes the process much faster, but also more reliable. We will be setting up some very simple build pipelines in this workshop, but IBM Cloud and other cloud platforms allow additional services to be added to a build pipeline to check for vulnerbailities, perform static quality analysis, monitor builds for issues, and perform other checks to make sure code being pushed to production is of high quality. 

1. Right-click when clicking on the **Deploy to IBM Cloud** buttons below to open them in a new tab:

<span style="font-size: 18pt">Frontend Angular Application: </span>[![Deploy to IBM Cloud](https://cloud.ibm.com/devops/setup/deploy/button.png)](https://cloud.ibm.com/devops/setup/deploy?repository=https://github.com/wkorando/cnsd-produce-client&branch=master&env_id=ibm:yp:us-south)

<span style="font-size: 18pt">Backend Spring Service: </span>[![Deploy to IBM Cloud](https://cloud.ibm.com/devops/setup/deploy/button.png)](https://cloud.ibm.com/devops/setup/deploy?repository=https://github.com/wkorando/cnsd-produce-service&branch=master&env_id=ibm:yp:us-south)

---
**Tip:** This deployment pipeline is being configured based on the template defined in the files under [.bluemix](/.bluemix). Because of time constraints, we won't cover the details of how these files work, but if you are interested you can [read more here](https://github.com/open-toolchain/).

---
	
2. For the Lite account users, make sure the region (1) is set to Dallas (us-south). Next, click (2) to configure the Delivery Pipeline.

	![](./images/pipeline-1.png)

3. Next create an API Key for this repo by clicking **Delivery Pipeline** and then the **Create** button. The default values that are generated should be fine.

	![](images/create_api_key.png)

4. Cick the **Create** button in the top right corner of the page.

	IBM Cloud will now take a few moments to initialize this toolchain. 

### Configuration as Code

Configuration as Code is a practice of co-locating configuration information about how an application should be executed in the same location where the code for the application is stored. This practices helps developers understand an application better by keeping all the important information about an application in a single area. This practice is also essential for building deployment pipelines as the pipelines will read this configuration information when deploying the application. Let's look at the configuration information in this project.

#### Dockerfile

As mentioned earlier every application running in Kubernetes is running in a container. Docker users Dockerfiles to describe how a container image should be built. The Dockerfile in this project looks like this:

```
FROM adoptopenjdk/openjdk8-openj9:alpine-slim

COPY target/produce-service.jar /

ENTRYPOINT ["java", "-jar", "produce-service.jar" ]
```

Let's step through what all these elements mean.

`FROM adoptopenjdk/openjdk8-openj9:alpine-slim`: This is describing which image to build this docker image off of. In this example we are building off a image provided by adoptopenjdk that contains the OpenJ9 JVM running Java 8. `alpine-slim` is a minimal Linux distro. 

`COPY target/produce-service.jar /`: This is copying the Java artifact we will be building into the docker image.

`ENTRYPOINT ["java", "-jar", "produce-service.jar" ]`: This is the command the image will run when it is started up.

#### Deployment.yml

`deployment.yml` files are used by Kubernetes to describe how an application should be deployed on a Kubernetes cluster. A deployment file is already present, below is the fully contents, let's take a closer look at its two major components.

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    run: produce-service
  name: produce-service
  namespace: default
spec:
  replicas: 2
  revisionHistoryLimit: 3
  selector:
    matchLabels:
      run: produce-service
  template:
    metadata:
      labels:
        run: produce-service
    spec:
      containers:
      - image: ${REGISTRY_URL}/${REGISTRY_NAMESPACE}/${IMAGE_NAME}:${IMAGE_TAG}
        name: produce-service
---
kind: Service
apiVersion: v1
metadata:
  labels:
    run: produce-service
  name: produce-service-port
  namespace: default
spec:
  selector:
    run: produce-service
  ports:
    - port: 8080
      name: http
```

##### Deploying an Application

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    run: produce-service
  name: produce-service
  namespace: default
spec:
  replicas: 2
  revisionHistoryLimit: 3
  selector:
    matchLabels:
      run: produce-service
  template:
    metadata:
      labels:
        run: produce-service
    spec:
      containers:
      - image: ${REGISTRY_URL}/${REGISTRY_NAMESPACE}/${IMAGE_NAME}:${IMAGE_TAG}
        name: produce-service
```

This section describes how an application should be deployed on the Kubernetes cluster. A [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) describes how a [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) should exist.

```
metadata:
  labels:
    run: produce-service
  name: produce-service
  namespace: default
```

This section is describing where a the deployment should exist and how it should be presented within the Kubernetes cluster (e.g. how it might be looked up by other Pods and Services within Kubernetes)

```
spec:
  replicas: 2
  revisionHistoryLimit: 3
  selector:
    matchLabels:
      run: produce-service
```

This section defines how many instances of this pod Kubernetes should maintain by default as well as how many previous revisions of a deployment Kubernetes should hold onto for rollback purposes. For scaling and reliability this is a very important area to understand for Kubernetes.

```
  template:
    metadata:
      labels:
        run: produce-service
    spec:
      containers:
      - image: ${REGISTRY_URL}/${REGISTRY_NAMESPACE}/${IMAGE_NAME}:${IMAGE_TAG}
        name: produce-service
```

Here we are defining the actual application we will be running. 

* `image` providing the coordinates in a container registry as to which image to run. 
* `terminationGracePeriodSeconds:` tells Kubernetes how long to wait before terminating a Pod that hasn't reached a healthy state.

##### Exposing an Application

```
kind: Service
apiVersion: v1
metadata:
  labels:
    run: produce-service
  name: produce-service-port
  namespace: default
spec:
  selector:
    run: produce-service
  ports:
    - port: 8080
      name: http
```

Kubernetes uses internal networking to allow pods, services, loadbalancers, etc. to communicate with  each other securely. However to have business value something needs to be exposed to the outside world. In the above section of YAML we are creating a [Service](https://kubernetes.io/docs/concepts/services-networking/service/), in this case a [NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport) to expose the `storm-tracker` application. This association is made with the `selector` text:

```
spec:
  selector:
    run: produce-service
```

By default Spring Boot runs on port `8080`, here we are telling the NodePort to direct external traffic through that port. We are also setting the protocol to use `http`. 

```
  ports:
    - port: 8080
      name: http
```  

### Pods and Pod to Pod Communication in Kubernetes

In Kubernetes applications run in a concept called a Pod. In the above deployment file much of what is being defined is so Kubernetes knows how to construct the Pods. If you run the following command: 

```
kubectl get pods
```

You will get back a list of pods currently running on your Kubernetes cluster. Which should look something like this: 

```

GOT PODS OUTPUT

```
---

**Tip:** It can take a few minutes for a project to be built and deployed. If you are not yet seeing pods with produce-service-* or produce-client-* wait a couple of minutes, or check the toolchains screen to see the current status of a deployment.  

---

When a new version of an application is deployed, kubernetes will create new pods instances and terminate the older pods. Pods might also become unhealthy in which case kubernetes might terminate that pod and spin up a new version. Because of this ephemeral nature of pods, using the IP address assigned to a pod to communicate with it doesn't make sense as this can change often. So instead to handle communication between pods within a kubernetes application we use services. 

##### Exposing an Application Internally in Kubernetes

```
kind: Service
apiVersion: v1
metadata:
  labels:
    run: produce-service
  name: produce-service-port
  namespace: default
spec:
  selector:
    run: produce-service
  ports:
    - port: 8080
      name: http
```

Kubernetes uses internal networking to allow pods, services, loadbalancers, etc. to communicate with  each other securely. However to have business value something needs to be exposed to the outside world. In the above section of YAML we are creating a [Service](https://kubernetes.io/docs/concepts/services-networking/service/), in this case a [NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport) to expose the `storm-tracker` application. This association is made with the `selector` text:

```
spec:
  selector:
    run: produce-service
```

By default Spring Boot runs on port `8080`, here we are telling the NodePort to direct external traffic through that port. We are also setting the protocol to use `http`. 

```
  ports:
    - port: 8080
      name: http
```

```
{
  "/api/*": {
    "target": "http://produce-service-port:8080",
    "secure": false,
    "loglevel": "debug"
  }
}
```


![](./images/kubernetes-networking.png)

##### Exposing an Application Externally in Kubernetes

```
kind: Service
apiVersion: v1
metadata:
  labels:
    run: produce-client
  name: produce-client-port
  namespace: default
spec:
  selector:
    run: produce-client
  ports:
    - port: 4200
      name: http
  type: LoadBalancer
```

Kubernetes uses internal networking to allow pods, services, loadbalancers, etc. to communicate with  each other securely. However to have business value something needs to be exposed to the outside world. In the above section of YAML we are creating a [Service](https://kubernetes.io/docs/concepts/services-networking/service/), in this case a [NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport) to expose the `storm-tracker` application. This association is made with the `selector` text:

```
spec:
  selector:
    run: produce-service
```

By default Spring Boot runs on port `8080`, here we are telling the NodePort to direct external traffic through that port. We are also setting the protocol to use `http`. 

```
  ports:
    - port: 8080
      name: http
```  


## 4. Reliable and Portable Integration Testing

In the previous section we built continuous delivery pipelines to handle the building and deployment of our applications to a kubernetes cluster. In enterprise the foundation on which continuous delivery is built is automated testing. Automated testing serves as a central step in verifying that a newversion of an application won't cause a problem when deployed to production. However organizations will only fully adopt continuous delivery if they are confident their automated test suites are prodiving accurate information.

In this section we will be exploring a notoriously difficult aspect of automated testing; integration testing. Integration test can be difficult because tests that depend upon a remote service are subject to state changes, a remote service could be; unavailable, its configuration could change, or data could have changed. This can lead to false negatives, tests failures where the cause of the failure isn't related to the scenario being tested.

If false negatives occur often enough this can lead test failures to being ignored, or tests being skipped altogether. Eventually this might lead to a bug being pushed to production because the test that was supposed to be covering it was ignore. If this happens enough an organization might decide to step back from continuous delivery. However there is another way!

In this section of the workshop we will look at using a couple of tools; Test Containers and Spring Cloud Contract for building integration tests that are reliable, portable, and give organizations confidence in their automated test suites allowing them to pursue continuous delivery. 

### Test Containers

Test containers is a library built that plugins into the popular JUnit library that allows for the construction and tearing down of a Docker container within the lifecycle of an automated test. 

### Spring Cloud Contract

Spring Cloud Contract is a library for performing contract driven testing. Contract driven testing can be a great way for ensuring a service backing an API and the clients consuming that API are using it correctly without having to depend on a live instance of that API being available. Let's look at writing some contracts for our API to see how contract driven design would work in practice. 


## 5. Connecting to Cloud Services

Cloud platforms provide a catalog of pre-defined services for addressing many of the common and not so common needs of enterprises. Services like database persistence, logging, observability, and more. For many organizations beyond being reliable, persistence and retrival isn't a differentiator. In these cases it can make sense for an organization to off-load the work of maintaining a database to a cloud provider, to better focus on areas of core business concerns. In this section of the workshop we will connect our applications and Kubernetes cluster to some of the service IBM Cloud provides. In the span of a few minutes we will have database access, logging aggregation, and metrics gathering capabilities. Capabilities that would often take weeks or months to configure and setup for many organizations. 

### Create a DB2 Database

With the IBM Cloud CLI, you can quick create new service instances with a single command. TO create a new database instance, in this case using DB2, run the following command:

```
ibmcloud resource service-instance-create spring-boot-db2 dashdb-for-transactions free us-south
```

### Handling the CRUD with Spring Data 

Spring Data is a popular library within the Spring Framework ecosystem. Using the Repostiroy pattern within Spring Data you can easily configure an application so that it is communicating with a database in mere minutes. We will be using Spring Data in this section to handle the database with the database we just created.  

1. Open your **pom.xml** update it adding these dependencies:

	```xml
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-jpa</artifactId>
	</dependency>
	<dependency>
	<groupId>com.ibm.db2.jcc</groupId>
		<artifactId>db2jcc</artifactId>
		<version>db2jcc4</version>
	</dependency>
	```

### Using Kubernetes Secrets to Connect to a Service

To connect to services our Spring Boot application will need connection information like username and password. Storing this information directly in `application.properties` files iss insecure and inconvenient. Kubernetes can securely store sensitive connection information like usernames and passwords in Kubernetes Secrets. You can then configure Kubernetes to supply these values when an application needs them. Let's configure Kubernetes to store the username and password to our new database in a secret: 


1. Open your terminal and run the following commandto create service credentials for connecting to the database we created at the start of this section:

	```
	ibmcloud resource service-key-create creds_Db2 Manager --instance-name spring-boot-db2
	```
	You will get output back that looks something like this:
	
	```	
	Name:          creds_Db2   
	ID:            crn:v1:bluemix:public:dashdb-for-transactions:us-south:a/4b4c36db94004c51b937b0343f8960f0:ec2f281c-5d4f-412e-a3ba-978882506e73:resource-key:1062ffab-c555-42c4-9c2d-c109520425b1   
	Created At:    Mon Nov  4 12:12:49 UTC 2019   
	State:         active   
	Credentials:                       
		       db:           BLUDB      
		       dsn:          DATABASE=BLUDB;HOSTNAME=dashdb-txn-sbox-yp-dal09-04.services.dal.bluemix.net;PORT=50000;PROTOCOL=TCPIP;UID=trv96241;PWD=b23lk8r-qnxwfbtm;      
		       host:         <host>      
		       hostname:     <host name>      
		       https_url:    https://dashdb-txn-sbox-yp-dal09-04.services.dal.bluemix.net      
		       jdbcurl:      jdbc:db2://dashdb-txn-sbox-yp-dal09-04.services.dal.bluemix.net:50000/BLUDB      
		       parameters:         
		       password:     <password>     
		       port:         50000      
		       ssldsn:       DATABASE=BLUDB;HOSTNAME=dashdb-txn-sbox-yp-dal09-04.services.dal.bluemix.net;PORT=50001;PROTOCOL=TCPIP;UID=trv96241;PWD=b23lk8r-qnxwfbtm;Security=SSL;      
		       ssljdbcurl:   jdbc:db2://dashdb-txn-sbox-yp-dal09-04.services.dal.bluemix.net:50001/BLUDB:sslConnection=true;      
		       uri:          db2://trv96241:b23lk8r-qnxwfbtm@dashdb-txn-sbox-yp-dal09-04.services.dal.bluemix.net:50000/BLUDB      
		       username:     <username>  
   	```
   
1. Create a file named **secret.yaml** and copy in the following: 

	```
	apiVersion: v1
	kind: Secret
	metadata:
	  name: db2-connection-info
	type: Opaque
	data:
	  username: <base64 username>
	  password: <base64 password>
	  jdbcurl: <base64 jdbcurl>
	```
1. Replace `<base64 *>` with their appropraite base64 encoded values:
	
	```
	echo -n 'VALUE' | base64
	```

1. In a terminal window in the same directory where you created **secret.yaml** run the following:

	```
	kubectl apply -f secret.yaml
	```

### Adding Test Data

1. Back on [IBM Cloud](https://cloud.ibm.com/), search for the DB2 instance you created earlier.

2. In the DB2 dashboard, click the **Open Console** button near the center of the page.

3. Click the "hamburger" button in the top left hand corner of the page (it's the three small green dashes)

4. Click **Run SQL** 

5. In the query box copy and execute the following: 

	```sql
	create sequence storms_id_generator start with 10 increment by 1 no maxvalue no cycle cache 24;
	
	create table storms (id int8 not null, end_date varchar(255), end_location varchar(255), intensity int4 not null, start_date varchar(255), start_location varchar(255), type varchar(255), primary key (id));
	
	insert into storms (id, start_date, end_date, start_location, end_location, type, intensity) values (storms_id_generator.NEXTVAL, '10-10-2018', '10-13-2018', 'Denver, Colorado', 'Kansas City, Missouri', 'Thunderstorm', 2);
	
	insert into storms (id, start_date, end_date, start_location, end_location, type, intensity) values (storms_id_generator.NEXTVAL, '01-15-2019', '01-17-2019', 'Atlantic Ocean', 'New York City, New York', 'Blizzard', 4);
	```

### Configuring the Application

1. Open **application.properties** in your project under `src/main/resources` and add the following:

	```
	spring.datasource.url=${jdbcurl}
spring.datasource.username=${username}
spring.datasource.password=${password}
	spring.datasource.driver-class-name=com.ibm.db2.jcc.DB2Driver
	spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.DB2Dialect
	spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
	#^^^prevents a warning exception from being thrown. See: https://github.com/spring-projects/spring-boot/issues/12007
	spring.jpa.open-in-view=false
	#^^^suppresses warning exception related to OSIV https://vladmihalcea.com/the-open-session-in-view-anti-pattern/
	```

2. Update the deployment.yml file with the following under `containers`

	```
        args: ["--spring.application.json=$(BINDING)"]
        env:
        - name: BINDING
          valueFrom:
            secretKeyRef:
              name: binding-living-on-the-cloud-db
              key: binding
	```  

---
**Tip:** See [here](deploymentFiles/2-deployment.yml) for a completed deployment.yml file with the above added.

---

2. Commit and push your changes to your repo:

	```
	git add .
	git commit -m "Added hello world url"
	git push
	```

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
* Better performance metering

In this section we will implement CQRS in our architecture through the usage of a kafka message service. 

## Extra Credit

-> use personal docker repository
-> use github repos
-> performance
