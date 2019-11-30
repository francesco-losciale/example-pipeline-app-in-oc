# OpenShift learning project

----

 TODO list
1. The pod fails for CrashLoopBackOff after being deployed. The Spring App does nothing to keep the container
alive. Read this if you plan to build a real app: 
    https://serverfault.com/questions/924243/back-off-restarting-failed-container-error-syncing-pod-in-minikube
2. livenessProbe and readinessProbe are commented out in the template. after adding Spring Actuator the deploy crashes for OOMKilled
3. trigger another build at the postCommit
4. improve README

---

This project contains an OpenShift template configuration to import in a new project 
the build configuration for a simple java app and for a Jenkins pipeline.

OpenShift Online is free and ideal for training/trial. 

Please refer to this page for a guide: 
* https://docs.okd.io/latest/dev_guide/index.html

OpenShift CLI is needed to follow these instructions.
Please download and install it following these steps: 
* https://docs.openshift.com/container-platform/3.6/cli_reference/get_started_cli.html

After creating your account and logging in, type these commands (you can also run them in a single batch)

```bash
oc delete project example-pipeline-project ; sleep 20;

oc new-project example-pipeline-project ;

oc new-app jenkins-persistent; 

oc new-app -f template.yaml --build-env='MAVEN_ARGS=-e -Popenshift -Dcom.redhat.xpaas.repo.redhatga package';

oc start-build example-java-app;
```

These commands will:
1. Create a new project in Open Shift
2. Create an app using the jenkins-persistent image from the Openshift registry
3. Import the template defined in this repository which creates:
    * A build configuration for a maven java app
    * A build configuration for a pipeline to build and deploy the java app 
4. Start the java app build


To start the app locally simply run `mvnw spring-boot:run`

---

Additional notes:

* MAVEN_ARGS is overwritten because the S2I tool provided by OpenShift does skip the tests when building the app
* To import Gradle projects you can use the jenkins-gradle-slave provided by the RedHat community (https://github.com/redhat-cop/containers-quickstarts). 
Unfortunately OpenShift Online is limited and does not allow you to use Docker strategy.
`Error from server: admission webhook "validate.build.create" denied the request: Builds with docker strategy are prohibited on this cluster`
* Take advantage of the OpenShift web platform when customising your first configuration. Then 
export the yaml template using this command (refinements will be needed): 

    `oc get --export all -o yaml > <path>/example-pipeline-app-in-oc/oc/template.yaml`

* When creating the app from the template file, oc will print all the parameter values on the stdout. 
You can find there the GITHUB_WEBHOOK_SECRET value you need to use as secret in the URL in GitHub.
You neeed to do this configuration manually. 
This way every push the build will automatically started with the new code.

* The GITHUB_WEBHOOK_SECRET value is the one to make the webhook URL difficult to find. 
 Do not mismatch it with the additional text field you can populate in GitHub to write the
 X-Hub-Signature header. It could be used to make the mechanism more secure, but OpenShift 
 still does not support it.
 Read this thread for further info: https://github.com/openshift/origin/issues/14674 

---
### Other useful commands

Import the object singularly from a file instead of an entire app template as above

`oc create -f oc/build-config.yaml ;`

Get the build configurations

`oc get bc ;`
    
Get more description on a specific BuildConfig (ie. github webhook)

`oc describe bc/example-java-app`    
    
Go to the Jenkins configuration

`<jenkins-external-url>/configure`


