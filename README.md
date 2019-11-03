# Create a template from an OC project  

The template contains all the API object configurations (build conf, 
deployment conf, services, pod, etc).

After you created a project in OC, proceed creating a custom application 
selecting an image from the OpenShift Developer Catalog.

For this examplethe image "Red Hat OpenJDK 8" has been selected.

Also, the Source-to-Image functionality in OpenShift enable you to
receive your code on git to construct the final image of the application.

Input: 
- Base image from the OpenShift Developer Catalog
- Git repository URL of your app 

Output:
- A new image with your app built on top of the base image

### Commands

`oc login ...`

`oc new-project`

... do all your setting on the web platform ...

`oc get --export all -o yaml > <path>/example-pipeline-app-in-oc/oc/template.yaml`

You have now obtained a template.yaml with all the API objects exported. You should 
make it prettier, removing unnecessary details and adding parameters.

