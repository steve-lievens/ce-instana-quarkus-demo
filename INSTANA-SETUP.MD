# Instana Setup

The easiest way to observe this application in Instana is to create a new Application perspective. From the Applications page create a new application perspective (button bottom right).

![new app perspective](img/application-perspective.png)

Filter this application to your zone or cluster (in my case the OpenShift cluster was in its own zone) and specify the namespace (aka project) you created above for this application.

![app filters](img/application-perspective-2.png)

Give the application a name and choose "All Calls".
