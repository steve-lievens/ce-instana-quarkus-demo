# Installation of the Demo Application

The installation will do a source to image (S2I) build for the Node.js and Quarkus based applications, so depending on the speed and I/O of your worker nodes it will take from 3 up to twenty minutes (and even a lot longer if you are deploying the natively compiled version) before all the applications are up and running.

The install will pull the source from these Git repo's and build the images with a multi-stage Dockerfile :

- [Node.js client](https://github.com/steve-lievens/ce-node-react-demo)
- [Quarkus Proxy](https://github.com/steve-lievens/ce-quarkus-proxy)
- [Quarkus Book](https://github.com/steve-lievens/ce-quarkus-book)

## Deploy the java version

To install download this repo locally and open a command line. Make sure to be logged into your OpenShift cluster with oc. Then issue :

For OpenShift v4.10

```
cd ocpv410
```

For OpenShift v4.10

```
cd ocpv412
```

Then continue the install with :
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

## Deploy the native binary version

This install is very similar as the one above, you just need to move into a different directory. So go up one level and issue below commands.
(You can give the OpenShift project a different name than the one below)

```
cd quarkus-native-otel-demo
oc new-project quarkus-native
oc apply -f .
```
