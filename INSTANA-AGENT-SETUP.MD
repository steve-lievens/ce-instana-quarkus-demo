# Setting up the Instana agent on OpenShift

This is actually clearly documented [here](https://www.ibm.com/docs/en/instana-observability/current?topic=requirements-installing-host-agent-openshift).

As you can see from the above documentation, there is an Operator based install, a Helm based install and then a simple deployment where you just throw a yaml file on the cluster.
All three methods finally result in a DaemonSet that created a Pod with the agent on each worker node.

To configure it, you can either update the Operand (for the operator install), your Helm chart or a Configmap for the simple deployment.

Here's an extract of what I changed for this demo :

```
    configuration_yaml: |
      com.instana.plugin.postgresql:
        user: 'quarkususer'
        password: 'quarkuspwd'
        database: 'publishers'
      com.instana.plugin.opentelemetry:
        enabled: false   # legacy setting
        grpc:
          enabled: true # takes precedence over legacy
        http:
          enabled: true # allows to enable http endpoints
```

The first part sets the credentials for the postgresql deployment, the second one enables the Open Telemetry capability.
