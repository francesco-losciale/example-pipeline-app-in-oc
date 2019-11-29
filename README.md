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

### Commands

`oc login ...`

`oc new-project project example-pipeline-app-in-oc`

... do all your setting on the web platform ...

`oc get --export all -o yaml > <path>/example-pipeline-app-in-oc/oc/template.yaml`

You have now obtained a template.yaml with all the API objects exported. You should 
make it prettier, removing unnecessary details and adding parameters.



----------
TODO

oc delete project example-pipeline-project ;
oc new-project example-pipeline-project ;
oc new-app jenkins-persistent; 
oc new-app -f oc/template.yaml


oc create -f oc/template.yaml ;
oc get bc ;
oc start-build example-pipeline-app-in-oc;



jenkins in oc: 
    oc new-app jenkins-persistent; 
    oc expose svc/jenkins;
    
    jenkins-external-url/configure <-- agents