# Quarkus Demo app for Instana monitoring

In order to investigate how a Quarkus app running on OpenShift behaves in the context of Instana observability, a demo application has been built.

The demo consists of two quarkus applications, a postgresql database and a nodejs client application. Below you see how it looks after you deploy it.

![Architecture](img/demo-architecture.png)

The Node.js application will start a continuous loop to call the Quarkus-proxy app every 5 seconds. This Quarkus-proxy app will then do two calls to the Quarkus-book app. One of these two calls will reach out to the postgresql database to get some data.

The Node.js application has been instrumented with the necessary Instana library to provide telemetry data. The Quarkus application has been instrumented with Open Telemetry, but this can be switched on or off. The postgresql database is an out of the box deployment, so you will get some Instana agents errors on it. I will explain later how you can get rid of them.

The Quarkus applications can be deployed as standard jvm based applications or as natively compiled apps. If you run Quarkus as a standard java application, the Instana monitoring is no different than any other java based application running in a JVM. But Quarkus can be compiled to a native binary that can be run standalone. In this case Instana has no out of the box sensor to capture data. That's why the Quarkus applications have been instrumented with Open Telemetry to get some data to Instana via this way.

The above setup allows you to compare the metrics from the default Instana jvm sensor against the Open Telemetry integration. At the time of writing, Open Telemetry support in Instana is still in beta, but it already provides some decent insights.

## Installation

The installation has been tested on OpenShift 4.10, so currently no guarantee it will work on another version. The installation will do a source to image build for the Node.js and Quarkus based applications, so it will take up to ten minutes (and even a lot longer if you are deploying the natively compiled version) before all the applications are up and running.

To install download this repo locally and navigate into the quarkus-jvm-demo folder with a command line. Make sure to be logged into your OpenShift cluster with oc. Then issue :

```
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

After starting, The Node.js app will automatically begin calling the Quarkus proxy, so you should see "movement" in Instana after that.
