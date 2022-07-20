# Openshift Template for Building Red Hat Container Images with IBM Semeru 11 Certified Edition

This Openshift template will take a Red Hat container image based on OpenJDK 11 and generate a new container by replacing the existing OpenJDK 11 with IBM Semeru 11 Certified Edition.

## The input parameters to the template:
  - OPENJDK_BASED_RED_HAT_CONTAINER_IMAGE - The OpenJDK 11 based Red Hat container image from the Red Hat Image Catalog.
    > E.g: registry.redhat.io/jboss-webserver-5/jws56-openjdk11-openshift-rhel8:latest
  - OPENJDK_BASED_RED_HAT_CONTAINER_IMAGESTREAM_NAME - The user defined imagestream name for the OpenJDK 11 based Red Hat container.
    > E.g: jws56-openjdk11-openshift-rhel8
  - SEMERU_BASED_RED_HAT_CONTAINER_IMAGESTREAM_NAME - The user defined imagestream name for the IBM Semeru 11 based Red Hat container.
    > E.g: jws56-openj9-11-openshift-rhel8
  - JAVA_TOOL_OPTIONS - The default Java Options provided by the IBM Semeru Runtimes 11 Certified Edition container image.
    > E.g: -XX:+IgnoreUnrecognizedVMOptions -XX:+PortableSharedCache -XX:+IdleTuningGcOnIdle -Xshareclasses:name=openj9_system_scc,cacheDir=/opt/java/.scc,readonly,nonFatal

## How It Works

The template defines three ImageStreams and a BuildConfig. There are 2 input ImageStreams, IBM Semeru 11 Certified container image and the OpenJDK 11 based Red hat container image that will periodically check to ensure that the images are up to date with their linked image catalogs. There is 1 output ImageStream for the newly created IBM Semeru 11 based Red Hat container image. The BuildConfig copies the `/opt` directory from the IBM Semeru 11 Certified container image into the OpenJDK 11 based Red hat container image and creates the new IBM Semeru 11 based Red Hat container image. The BuildConfig will automatically rebuild upon any config changes or if any input ImageStreams are updated.

## Deploying the Openshift Template

Here are some two examples to deploy the template into Openshift. 

The first method uses command-line parameters:
```
oc project <project-name>
oc new-app -f openjdk-to-semeru-transition.json \
-p OPENJDK_BASED_RED_HAT_CONTAINER_IMAGE=registry.redhat.io/jboss-webserver-5/jws56-openjdk11-openshift-rhel8:latest \
-p OPENJDK_BASED_RED_HAT_CONTAINER_IMAGESTREAM_NAME=jws56-openjdk11-openshift-rhel8 \
-p SEMERU_BASED_RED_HAT_CONTAINER_IMAGESTREAM_NAME=jws56-openj9-11-openshift-rhel8
```

The second method uses the command-line with a parameter file **params.env**:
```
OPENJDK_BASED_RED_HAT_CONTAINER_IMAGE=registry.redhat.io/jboss-webserver-5/jws56-openjdk11-openshift-rhel8:latest
OPENJDK_BASED_RED_HAT_CONTAINER_IMAGESTREAM_NAME=jws56-openjdk11-openshift-rhel8
SEMERU_BASED_RED_HAT_CONTAINER_IMAGESTREAM_NAME=jws56-openj9-11-openshift-rhel8
```
```
oc project <project-name>
oc process -f openjdk-to-semeru-transition.json --param-file=params.env | oc create -f -
```
