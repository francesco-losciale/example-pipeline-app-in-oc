# Create a template from an OC project  

OpenShift allows you to use the interface from the web platform to set up a project so that 
once you completed all your customisation (for example using OpenShift Developer Catalog),
you can dump all the configuration in a yaml template file.

The template file would contain all the API objects' configuration needed to run a service 
in OC (build conf, deployment conf, services, pod, etc).

Beside that, the Source-to-Image functionality in OpenShift enable you to create an image
starting from your personal git repository with the codebase of your app.

Input: 
- Base image creted using the OpenShift Developer Catalog
- Git repository URL of your app 

Output:
- A new image with your app built on top of the base image

### Exporting the Objects after customising them on the Open Shift web platform

`oc login ...`

`oc new-project project example-pipeline-app-in-oc`

... do all your setting on the web platform ...

`oc get --export all -o yaml > <path>/example-pipeline-app-in-oc/oc/template.yaml`

You have now obtained a template.yaml with all your objects exported. You should 
make them prettier, removing unnecessary details or adding parameters.

### Creating a BuildConfig for a Java app together with a BuildConfig for a pipeline

Open Shift Online platform, which is provided for free training/trial purposes, has 
some limitation. 
S2I feature does not provide can scan Maven projects but does not provide anything for Gradle.

Another option would have been to use a jenkins-slave-gradle image provided by the Red Hat 
community:

`https://github.com/redhat-cop/containers-quickstarts`

Open Shift Online though does not allow you to build images with Docker strategy.

`Error from server: admission webhook "validate.build.create" denied the request: Builds with docker strategy are prohibited on this cluster`

So switching from Gradle to Maven was necessary for our experiment.

Another addendum: by default S2I maven build the artifact skipping the tests.
To make them part of the build it's necessary to overwrite the MAVEN_ARGS variable
when importing the template (see below).

The following commands can be used to:
1. Create a new project in Open Shift
2. Create an app using the jenkins-persistent image from the Openshift registry
3. Import the template defined in this repository which creates:
    * A build configuration for a maven java app
    * A build configuration for a pipeline to build and deploy the java app 
4. Start the java app build
 

```bash
oc delete project example-pipeline-project ; sleep 20;

oc new-project example-pipeline-project ;

oc new-app jenkins-persistent; 

oc new-app -f template.yaml --build-env='MAVEN_ARGS=-e -Popenshift -Dcom.redhat.xpaas.repo.redhatga package';

oc start-build example-java-app;
```

### Setting the GitHub webhook

When creating the app from the template file, oc will print all the parameters
generated for it. You can find there the GITHUB_WEBHOOK_SECRET value you need
to use as secret in the URL in GitHub.
Just a note: the secret we are talking about is the one to make the webhook URL
difficult to find. 
In Github there's an additional text field you can populate to write the
X-Hub-Signature which could be used to make the mechanism more secure.
OpenShift though is still ignoring this value.
Please read this thread: https://github.com/openshift/origin/issues/14674

### Other useful commands

Import the object singularly from a file instead of an entire app template as above

`oc create -f oc/build-config.yaml ;`

Get the build configurations

`oc get bc ;`
    
Get more description on a specific BuildConfig (ie. github webhook)

`oc describe bc/example-java-app`    
    
Go to the Jenkins configuration

`<jenkins-external-url>/configure`


### Starting the app locally

`mvn spring-boot:run`

### TODO
1. fix - the pod running the java app crashes and restart continuously (CrashLoopBackOff)
2. Git hooks (https://docs.okd.io/latest/dev_guide/builds/triggering_builds.html)
    https://github.com/openshift/origin/issues/14674 OpenShift is not very secure with hooks
3. trigger another build at the postCommit
2. improve README
