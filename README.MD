# Quarkus Demo app for Instana monitoring

In order to investigate how a Quarkus app running on OpenShift behaves in the context of Instana observability, a demo application has been built.

The demo consists of two quarkus applications, a postgresql database and a nodejs client application. Below you see how it looks after you deploy it.

![Architecture](img/demo-architecture.png)

The Node.js application will start a continuous loop to call the Quarkus-proxy app every 5 seconds. This Quarkus-proxy app will then do two calls to the Quarkus-book app. One of these two calls will reach out to the postgresql database to get some data.

The Node.js application has been instrumented with the necessary Instana library to provide telemetry data. The Quarkus application has been instrumented with Open Telemetry, but this can be switched on or off. The postgresql database is an out of the box deployment, so you will get some Instana agents errors on it. I will explain later how you can get rid of them.

The Quarkus applications can be deployed as standard jvm based applications or as natively compiled apps. If you run Quarkus as a standard java application, the Instana monitoring is no different than any other java based application running in a JVM. But Quarkus can be compiled to a native binary that can be run standalone. In this case Instana has no out of the box sensor to capture data. That's why the Quarkus applications have been instrumented with Open Telemetry to get some data to Instana via this way.

The above setup allows you to compare the metrics from the default Instana jvm sensor against the Open Telemetry integration. At the time of writing, Open Telemetry support in Instana is still in beta, but it already provides some decent insights.

## Prerequisites

In order to run this demo you will need an OpenShift cluster version 4.10, so currently no guarantee that it will work on another version.
To see the results in Instana, you will need to instrument this cluster with the Instana agents and have access to an Instana environment where these agents will report into.

## Installation

The installation will do a source to image (S2I) build for the Node.js and Quarkus based applications, so depending on the speed and I/O of your worker nodes it will take from 3 up to twenty minutes (and even a lot longer if you are deploying the natively compiled version) before all the applications are up and running.

The install will pull the source from these Git repo's and build the images with a multi-stage Dockerfile :

- [Node.js client](https://github.com/steve-lievens/ce-node-react-demo)
- [Quarkus Proxy](https://github.com/steve-lievens/ce-quarkus-proxy)
- [Quarkus Book](https://github.com/steve-lievens/ce-quarkus-book)

### Deploy the java version

To install download this repo locally and open a command line. Make sure to be logged into your OpenShift cluster with oc. Then issue :
(You can give the OpenShift project a different name than the one below)

```
cd quarkus-jvm-demo
oc new-project quarkus-java
oc apply -f .
```

This will create all the necessary objects at once. You should see something like this :

```
buildconfig.build.openshift.io/bc-nodeclient created
service/nodeclient created
deployment.apps/nodeclient created
imagestream.image.openshift.io/nodeclient created
route.route.openshift.io/nodeclient created
buildconfig.build.openshift.io/bc-quarkus-book created
service/quarkus-book created
deployment.apps/quarkus-book created
imagestream.image.openshift.io/quarkus-book created
statefulset.apps/postgres created
service/postgresql created
buildconfig.build.openshift.io/bc-quarkus-proxy created
service/quarkus-proxy created
deployment.apps/quarkus-proxy created
imagestream.image.openshift.io/quarkus-proxy created
```

Three deployments (for Node.js and the two Quarkus apps) and one statefulset (for Postgresql) will be created. Postgreql will simply pull an existing container from the Red Hat registry, but for the other ones a build will be started. Please check if these builds all run fine as it can happen that a build fails due to insufficient resources on the system. When that happens, simply start a new build for the failed one.

After starting, The Node.js app will automatically begin calling the Quarkus proxy, so you should see "movement" in Instana after that.

### Deploy the native binary version

This install is very similar as the one above, you just need to move into a different directory. So go up one level and issue below commands.
(You can give the OpenShift project a different name than the one below)

```
cd quarkus-native-otel-demo
oc new-project quarkus-native
oc apply -f .
```

## Instana setup

The details on how to setup the Instana side can be found [here](INSTANA-SETUP.MD).

## References

- [Quarkus official website](https://quarkus.io)
