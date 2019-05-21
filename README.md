Agenda
======

[CHALLENGE 1: Create and manage projects](#challenge--2-create-and-manage-projects)

[CHALLENGE 2: Create and manage Applications](#challenge--3-create-and-manage-applications)

[CHALLENGE 3: Configuring automated builds](#challenge--4-configuring-automated-builds)

[CHALLENGE 4: Continuous deployment](#challenge--5-continuous-deployment)

[CHALLENGE 5: Mini project: JBOSS EAP application](#challenge--6-mini-project-jboss-eap-application)

Objectives and pre-requisites
=============================

This document describes the introductory steps how to work with OpenShift Container Platform
on Azure. The lab is based on OpenShift version 3.10.

You need to have an account on [GitHub](http://www.github.com/), If
you don't have one create a free account (<https://github.com/>).

The Lab challenges cover:

-   Introduction to OpenShift3

-   Creating and managing OpenShift projects on Azure

-   Creating and managing OpenShift applications on Azure

-   Automating builds with Linux Containers on Azure.

-   Mini project: Jboss EAP application

Introduction to openshift
=========================

[OpenShift](http://www.openshift.com) containers platform is Red Hat's Platform-as-a-Service (PaaS) on top of Kubernetes. 
It allows developers to quickly develop, host, and scale applications in a cloud environment and have Enterprise ready Kubernetes distribution.

Microsoft and Red Hat have signed a partnership that includes support to run Red Hat OpenShift on Microsoft Azure and Azure Stack. That will be 
expanded to fully managed solution soon.

OpenShift offers multiple access modes including: developer CLI, admin CLI, web console and IDE plugins. Click2cloud is a plugin that allows Visual studio to deploy code to OpenShift, directly.

![](./img/image1.jpg)

CHALLENGE 1: Create and manage projects 
=========================================

First we will learn how to create a project(namespace in Kubernetes). In  this challenge we will focus on the graphical portal.

To create an *application*, you must first create a new *project*, which will contain the application.

1.  From your browser, visit the OpenShift web console at *https://master.bratislava-497d.openshiftworkshop.com/console*. The web site, uses a self-signed certificate,
so if prompted, continue and ignore the browser warning.

![](./img/image3.jpg)

2.  Log in using your username and password.

![](./img/image4.jpg) 

3.  To create a new project, click on blue *Create Project* button.

2.  Type a *unique* name, display name, and description for the new project.

5.  Click Create. The web console's welcome screen should start loading.

6.  Click on the project name in right column and examine your fresh project.

![](./img/image5.jpg)

CHALLENGE 2: Create and manage Applications
============================================

Before we begin we will fork OpenShift example application on GitHub. We will use OpenShift
Django Example application as it is quick to build and easy to edit.

1.  Login to your *github* account, or create one if you didn't.

2.  Browse to *https://github.com/openshift/django-ex.git* repository and fork it into your *github* account

![](./img/image2.jpg)

Then we will return to OpenShift console and choose *Browse Catalog* to be able to select
predefined application types. 

1.  We will choose *Python* application form an application menu.
    (You can use filtering as shown on next screenshot)

![](./img/image6.jpg)

2.  Select *Python* image type.

![](./img/image7.jpg)
 
3.  Click *Next* and then type a name for your application, and specify the git repository URL you previously forked: <https://github.com/'your_github_username'/django-ex.git>.

![](./img/image8.jpg)
 
4. Click create to build your Django application. You can watch it on *Overview* page.

![](./img/image9.jpg)

5. When build is finished it should look like:

![](./img/image10.jpg)

OpenShift leverages the Kubernetes concept of a pod, which is one or more containers deployed together on one host. A pod is the smallest compute unit that can be defined, deployed, and managed.

Pods are the rough equivalent of a machine instance (physical or virtual) to a container. Each pod is allocated its own internal IP address, therefore owning its entire port space. Containers within pods can share their local storage and networking.

While the Python *pod* is being created, its status is shown as pending.
The Python *pod* then starts up and displays its newly-assigned IP address. When the Python *pod* is running, the build is complete.

Pods have a lifecycle; they are defined, then they are assigned to run on a node, then they run until their container(s) exit or they are removed for some other reason. Pods, depending on policy and exit code, may be removed after exiting, or may be retained in order to enable access to the logs of their containers.

8. From the overview page, click the web address for the application in the upper right corner. Verify that the web application is up and available.

![](./img/image11.jpg)

9.  Return to the *OpenShift* admin console. Browse to the project's overview page, and test scaling out and in your application by increasing or decreasing the number of *pods*, using the up and down arrow signs on the web console.
Scale out the app into 3 pods and watch the progress.

![](./img/image12.jpg)
    
10.  Browse to Applications -> Pods, and make sure 3 pods serving the same application are now up and running.

![](./img/image13.jpg)
    

CHALLENGE 3: Configuring automated builds
==========================================

Since we forked the source code of the application from the [OpenShift GitHub repository](https://github.com/openshift/django-ex), we can use a *webhook* to automatically trigger a rebuild of the application whenever code changes are pushed to the forked repository.

To set up a *webhook* for your application:

1.  From the Web Console, navigate to the project containing your application.

2.  Click the Browse tab, then click Builds -> Builds.

3.  Click your build name, then click the Configuration tab.

4.  Click next to GitHub webhook URL to copy your *webhook* payload URL.

![](./img/image14.jpg)
 
5.  Navigate to your forked repository on GitHub, then click Settings.

6.  Click Webhooks and Click Add webhook.

7.  Paste your *webhook* URL into the Payload URL field.

8.  As Content Type choose application/json

9.  Disable SSL verification and click Add webhook to save.

![](./img/image15.jpg)

10. Keep all the other parameters with the default values.

GitHub will now attempt to send a ping payload to your *OpenShift* server to ensure that communication is successful. If you see a green check mark appear next to your *webhook* URL, then it is correctly configured.

Hover your mouse over the check mark to see the status of the last delivery.

![](./img/image16.jpg)

Next time you push a code change to your forked repository, your application will automatically rebuild.

CHALLENGE 4: Continuous deployment 
====================================

In this section, we demonstrate one of the most powerful features of *OpenShift*. We will see how we can trigger a continuous deployment pipeline, just by committing code change to Github.

Once there is a code change, the Github *webhook* will trigger the build of a new container image that combines a blueprint image from the registry with the updated code and generate a new image. This feature is called *S2I*, or source to image. Once the build finishes, *OpenShift* will automatically deploy the new application based on the new image. This capability enables multiple deployment strategies such
as A/B testing, Rolling upgrades...

![](./img/image17.jpg)

1.  Use Azure cloud shell or install *Git* into your local machine

PS: If you don't want to use *git*, you still can perform this challenge by moving to step-5 and editing the *index.html* file , directly from the web interface of *github* and then go to step-7.
Create a "dev" folder and change into.

```bash
 $ mkdir dev && cd dev
```

2.  Clone the forked repository to your local system

```bash
 $ git clone https://github.com/<YourGithubUsername>/django-ex.git
```

3.  Make sure your local *git* repository is referencing to your
    *django-ex git*, on *github*:

```bash
 $ cd django-ex
```

```bash
 $ git remote -v
```

4.  On your local machine, use your preferred text editor to change the sample application's source for the file *welcome/templates/welcome/index.html*

Make a code change that will be visible from within your application.
For example: on line 215, change the title to "Welcome to your Django application on OpenShift on Azure!", then save your changes.

5.  Verify the working tree status

```bash
 $ git status
```

6.  Add index.html content to the index, Commit the change in *git*, and push the change to your fork. You will need to authenticate with your *github* credentials


```bash
 $ git add welcome/templates/welcome/index.html
 $ git commit -m "Changed welcome message to reflect Azure runtime."
 $ git status
 $ git push
```

7.  If your *webhook* is correctly configured, your application will immediately rebuild itself, based on your changes. Monitor the build from the graphical console. Once the rebuild is successful, view your updated application using the route that was created earlier.
Now going forward, all you need to do is push code updates and OpenShift handles the rest.

1.  In an *Overview* view you can see your deployment beeing updated.
![](./img/image19.jpg)

1.  In *Build->Build* view you can see details of your webhook build.
![](./img/image18.jpg)

8.  From the web browser refresh the page and note the new change

![](./img/image20.jpg)


9.  You may find it useful to manually rebuild an image if your *webhook* is not working, or if a build fails and you do not want to change the code before restarting the build. To manually rebuild the image based on your latest committed change to your forked repository:

    a.  Click the Browse tab, then click Builds.

    b.  Find your build, then click Start Build.


CHALLENGE 5: Mini project: JBOSS EAP application
=================================================

In this mini project, we will deploy a three tiers application consisting of Leaflet mapping front end with a JaxRS and MongoDB back end. The application, allows to visualize the locations of major National Parks and Historic Sites (https://github.com/OpenShiftDemos/restify-mongodb-parks).

During this challenge, we will leverage the CLI tool of OpenShift.

1.  Download and install the OpenShift CLI related to your operating system. The easiest way to download the CLI is by accessing the About page on the web console if your cluster administrator has enabled the download links. Another alternative is to ssh into your bastion host that has the CLI tool installed already.

If you don't have a valid Red Hat subscription, you could still download the oc tool from: https://github.com/openshift/origin/releases/tag/v3.11.0
2.  Use your openshift url endpoint to login to your environment from the CLI

``` bash
oc login https://masterdnsv3ebx5ljrh67e.eastus.cloudapp.azure.com
The server uses a certificate signed by an unknown authority.
You can bypass the certificate check, but any data you send to the server could be intercepted by others.
Use insecure connections? (y/n): y

Authentication required for https://masterdnsv3ebx5ljrh67e.eastus.cloudapp.azure.com:443 (openshift)
Username: ocpuser1
Password: 
Login successful.

You have one project on this server: "ocupser1"

Using project "ocupser1".
``` 

3.  Create a new project "nationalparks" with a custom suffix

```bash
oc new-project nationalparks${suffix}
```

4.  From the web console, add a new Java application using the following git lab repository <https://gitlab.com/gshipley/nationalparks.git>

![](./img/image21.jpg)

![](./img/image22.jpg)

Please make sure to select the `master` branch for cloning the project and the Context Dir to empty.

5.  List builds operations:

```bash
oc get builds
```

6.  List existing projects, pods and view logs in real time:

```bash
oc get projects
```

7.  List existing pods

```bash
oc get pods -w
```

1.  List logs in real time

```bash
oc logs -f nationalparklocator-1-build
```
    
    
7.  Get url form *Overview* and browse the newly created application and verify it is available

![](./img/image23.jpg)

8.  We can see the map but not the attraction points.The reason is that we only deployed the front-end application. What we will need now is to add a backend data base. For demo purposes, please choose mongodb with ephemeral storage.

![](./img/image24.jpg)

![](./img/image25.jpg)
    
![](./img/image26.jpg)

9.  The graphical portal should show two applications in our project

![](./img/image27.jpg)

11. Change the deployment configuration of the front-end application to include the environment variables required to access the database

```bash
oc set env dc nationalparklocator -e MONGODB_USER=mongodb MONGODB_PASSWORD=mongodb -e MONGODB_DATABASE=mongodb
```

12. verify the last modification took place by running 

```bash
$ oc get dc nationalparklocator -o json
```

13. Back to the graphical console, note the automatic migration to a new pod based on the new configuration

![](./img/image28.jpg)

    
14. Navigate to the application end-point and verify that the parks are now showing on the map

![](./img/image29.jpg)
    
15. Our new application became very popular, and we need to scale out our front end to two pods. Use "oc scale" to do it

```bash
oc get dc
oc scale --replicas=2 dc/nationalparklocator
oc get dc
```

16. Now, let's test the self-healing, capabilities of OpenShift by deleting one of the running pods. Because, the desired state of the replication controller is 2 pods for the application "nationalparklocator", OpenShift will automatically and instantly trigger the deployment of a new pod.
    
```bash
oc get pods
oc delete pod nationalparklocator-2-2cqz2
oc get pods
```

