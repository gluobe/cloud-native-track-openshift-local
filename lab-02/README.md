# Lab 02 - Creating an app using Docker build

In this exercise we will learn how to create an application from a Dockerfile.
OpenShift takes Dockerfile as an input and generates your application Docker
image for you.

## Task 1: Create a project

For this lab we are going to create fresh project. Use the following command.

```
oc new-project lab-02
```

## Task 2: Creating an app using a Dockerfile

This time we will use a project that has a Dockerfile in a source code
repository. We will use a simple project on github https://github.com/gluobe/time.
The `rhel` folder from this github project is built starting with rhel7 as the
base image which is described in Dockerfile. Look at the Dockerfile for this
project. It starts off with `registry.access.redhat.com/rhel7` image. It copies
the source code which is a simple `init.sh` file and exposes port `8080`.
Look at the `init.sh` that just displays the current datetime. There is also a
PHP version of the same project available in the php folder if you like to use
that. The php version does exactly the same it has a `time.php` file that
displays the time.

*Docker Build*: When OpenShift finds a Dockerfile in the source, it uses this
Dockerfile as the basis to create a docker image for your application. This
strategy is called `Docker Build` strategy on OpenShift. We’ll see more about it
when we look at the build configuration a couple of steps down the line. Once
OpenShift builds the application’s docker image, it stores that in a local
docker registry. Later it uses this image to deploy an application that runs in
a pod.

Now let’s create an application using this approach. We will run `oc new-app`
command by supplying the git uri as the parameter.

```
oc new-app https://github.com/gluobe/time --context-dir=rhel

---

--> Found container image a32ff6d (5 weeks old) from registry.access.redhat.com for "registry.access.redhat.com/rhscl/httpd-24-rhel7"

    Apache httpd 2.4 
    ---------------- 
    Apache httpd 2.4 available as container, is a powerful, efficient, and extensible web server. Apache supports a variety of features, many implemented as compiled modules which extend the core functionality. These can range from server-side programming language support to authentication schemes. Virtual hosting allows one Apache installation to serve many different Web sites.

    Tags: builder, httpd, httpd24

    * An image stream tag will be created as "httpd-24-rhel7:latest" that will track the source image
    * A Docker build using source code from https://github.com/gluobe/time will be created
      * The resulting image will be pushed to image stream tag "time:latest"
      * Every time "httpd-24-rhel7:latest" changes a new build will be triggered

--> Creating resources ...
    imagestream.image.openshift.io "httpd-24-rhel7" created
    imagestream.image.openshift.io "time" created
    buildconfig.build.openshift.io "time" created
    deployment.apps "time" created
    service "time" created
--> Success
    Build scheduled, use 'oc logs -f buildconfig/time' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose service/time' 
    Run 'oc status' to view your app.
```

You’ll notice that OpenShift created a few things at this point. You will find a
buildconfig, deploymentconfig, service and imagestreams in the above list. The
application is not running yet. It needs to be built and deployed. Within a
minute or so, you will see that OpenShift starts the build.

## Task 3: Build

In the meantime lets have a look at the buildconfig by running the command
shown below.

```
oc get bc time -o yaml

---

apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  annotations:
    openshift.io/generated-by: OpenShiftNewApp
  creationTimestamp: "2022-05-16T13:08:58Z"
  generation: 2
  labels:
    app: time
    app.kubernetes.io/component: time
    app.kubernetes.io/instance: time
  name: time
  namespace: lab-02
  resourceVersion: "148500"
  uid: caf78f0b-e62d-4f07-aa1b-d3b911d62141
spec:
  failedBuildsHistoryLimit: 5
  nodeSelector: null
  output:
    to:
      kind: ImageStreamTag
      name: time:latest
  postCommit: {}
  resources: {}
  runPolicy: Serial
  source:
    contextDir: rhel
    git:
      uri: https://github.com/gluobe/time
    type: Git
  strategy:
    dockerStrategy:
      from:
        kind: ImageStreamTag
        name: httpd-24-rhel7:latest
    type: Docker
  successfulBuildsHistoryLimit: 5
  triggers:
  - github:
      secret: 42RV-Ch2gn14BMxX4EWk
    type: GitHub
  - generic:
      secret: KhQ7ON1xvWtSznlfamty
    type: Generic
  - type: ConfigChange
  - imageChange: {}
    type: ImageChange
status:
  imageChangeTriggers:
  - from:
      name: httpd-24-rhel7:latest
    lastTriggerTime: "2022-05-16T13:09:00Z"
    lastTriggeredImageID: registry.access.redhat.com/rhscl/httpd-24-rhel7@sha256:5298ccf71fbb379e84c712517854bca62ae9da08c7ad20f629cf9dae611448ed
  lastVersion: 1
```

Note the name of the buildconfig in metadata is set to `time`, the git uri
pointing to the value you gave while creating the application. Also note the
Strategy.type set to `Docker`. This indicates that the build will use the
instructions in this Dockerfile to do the docker build.

Build starts in a minute or so. You can view the list of builds using
`oc get builds` command. You can also start the build using
`oc start-build time` where `time` is the name we noticed in the buildconfig.

```
oc get builds

---

NAME      TYPE      FROM          STATUS     STARTED          DURATION
time-1    Docker    Git@1ec2d66   Complete   19 minutes ago   1m13s
```

Note the name of the build that is running i.e. time-1. We will use that name to
look at the build logs. Run the command as shown below to look at the build
logs. This will run for a few mins. At the end you will notice that the docker
image is successfully created and it will start pushing this to OpenShift’s
internal docker registry.

```
oc logs build/time-1

---

<output ommited>
Successfully built 492e4a3bf772
Pushing image image-registry.openshift-image-registry.svc:5000/lab-02/time:latest ...
Getting image source signatures
Copying blob sha256:63308c6e36e14780fcf5dd36028d320cece927f1e3892230c77356e0c792ff10
Copying blob sha256:58b0162c6661b8cbfb4d889a8d45a05dbd80f6c6ec125bed2d5db54e58eea63a
Copying blob sha256:deda48287c30fcbc2b399bbaf4218b911caece4ed2764257a9b9b340ada9a9e1
Copying blob sha256:e4e8bbae6e3414361370b2ac12224e2310656cd1a5112119719af2838c10ea39
Copying blob sha256:eea5ac07d489515cf1d0aade621f468cc8f2250658fd1ccb6da41dee63601112
Copying config sha256:38067d7a31566f2d7124ad4c882fc209ad5369572aab6c01998136f5a11b3d76
Writing manifest to image destination
Storing signatures
Successfully pushed image-registry.openshift-image-registry.svc:5000/lab-02/time@sha256:f50629389354899fe2633fcddcbaf674b4aa8790002287b6eb056f919a0ba649
Push successful
```

In the above log note how the image is pushed to the local docker registry. The
registry is running at `image-registry.openshift-image-registry.svc` at port `5000`.

## Task 4: Deployment

Once the image is pushed to the docker registry, OpenShift will trigger a deploy
process. Let us also quickly look at the deployment configuration by running the
following command.

```
oc get deployment -o yaml

---

apiVersion: v1
items:
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    annotations:
      deployment.kubernetes.io/revision: "2"
      image.openshift.io/triggers: '[{"from":{"kind":"ImageStreamTag","name":"time:latest"},"fieldPath":"spec.template.spec.containers[?(@.name==\"time\")].image"}]'
      openshift.io/generated-by: OpenShiftNewApp
    creationTimestamp: "2022-05-16T13:08:58Z"
    generation: 2
    labels:
      app: time
      app.kubernetes.io/component: time
      app.kubernetes.io/instance: time
    name: time
    namespace: lab-02
    resourceVersion: "148804"
    uid: c0c8bd33-53fa-485a-ba72-4ab3ba4cc6eb
  spec:
    progressDeadlineSeconds: 600
    replicas: 1
    revisionHistoryLimit: 10
    selector:
      matchLabels:
        deployment: time
    strategy:
      rollingUpdate:
        maxSurge: 25%
        maxUnavailable: 25%
      type: RollingUpdate
    template:
      metadata:
        annotations:
          openshift.io/generated-by: OpenShiftNewApp
        creationTimestamp: null
        labels:
          deployment: time
      spec:
        containers:
        - image: image-registry.openshift-image-registry.svc:5000/lab-02/time@sha256:f50629389354899fe2633fcddcbaf674b4aa8790002287b6eb056f919a0ba649
          imagePullPolicy: IfNotPresent
          name: time
          ports:
          - containerPort: 8080
            protocol: TCP
          - containerPort: 8443
            protocol: TCP
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
  status:
    availableReplicas: 1
    conditions:
    - lastTransitionTime: "2022-05-16T13:10:13Z"
      lastUpdateTime: "2022-05-16T13:10:13Z"
      message: Deployment has minimum availability.
      reason: MinimumReplicasAvailable
      status: "True"
      type: Available
    - lastTransitionTime: "2022-05-16T13:08:58Z"
      lastUpdateTime: "2022-05-16T13:10:13Z"
      message: ReplicaSet "time-56ffc7fcf8" has successfully progressed.
      reason: NewReplicaSetAvailable
      status: "True"
      type: Progressing
    observedGeneration: 2
    readyReplicas: 1
    replicas: 1
    updatedReplicas: 1
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

Note where the image is picked from. It shows that the deployment picks the
image from the local registry (same ip address and port as in buildconfig) and
the image tag is same as what we built earlier. This means the deployment step
deploys the application image what was built earlier during the build step.

If you get the list of pods, you’ll notice that the application gets deployed
quickly and starts running in its own pod.

```
oc get pods

---

NAME                    READY   STATUS      RESTARTS   AGE
time-1-build            0/1     Completed   0          3m59s
time-56ffc7fcf8-7ppnt   1/1     Running     0          3m1s
```

## Task 5: Adding route

This step is very much the same as what we did in the previous exercise. We will
check the service and add a route to expose that service.

```
oc get services

---

NAME      CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
time      172.30.xx.82   <none>        8080/TCP   2h
```

Here we expose the service as a route.

```
oc expose service time

---

route.route.openshift.io/time exposed
```

And then we check the route exposed.

```
oc get routes

---

NAME   HOST/PORT                      PATH   SERVICES   PORT       TERMINATION   WILDCARD
time   time-lab-02.apps-crc.testing          time       8080-tcp                 None
```

## Task 6: Running the application

Now run the application by using the route you provided in the previous step.
You can use either curl or your browser. The application displays a success
message.

```
curl time-lab-02.apps-crc.testing

---

Congratulations you just deployed your app by using a Docker build strategy!
```

Congratulations!! In this exercise you have learnt how to create, build and
deploy an application using OpenShift’s `Docker Build strategy`.

## Task 7 : Delete your project

Delete the project with the following command.

```
oc delete project lab-02

---

project.project.openshift.io "lab-02" deleted
```