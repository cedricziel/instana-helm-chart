# Instana

[Instana](https://www.instana.com/) is a Dynamic APM for Microservice Applications

## Introduction

This chart adds the Instana Agent to all nodes in your cluster via a DaemonSet.

## Prerequisites for Helm

To use this, first install `helm` and tiller (the Kubernetes cluster-side tool helm talks to) using the standard documentation:

[https://docs.helm.sh/using_helm/#installing-helm](https://docs.helm.sh/using_helm/#installing-helm)

Most Kubernetes clusters have RBAC enabled, therefore, create a service account for helm to use, for example

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

```bash
$ kubectl create -f tiller-service-account.yaml
```

Install tiller to your cluster with 

```bash 
$ helm init --service-account tiller
```

## Installing the Chart

To configure the installation you can either specify the options on the command line using the **--set** switch, or you can edit **values.yaml**. Either way you should ensure that you set values for:

* instana.agent.key
* instana.agent.endpoint.host
* instana.agent.endpoint.port
* instana.zone

Check the values for the endpoint entries in [agent backend configuration](https://docs.instana.io/quick_start/agent_configuration/#backend)

To install the chart with the release name `instana-agent` and set the values on the command line run:

```bash
$ helm install . --name instana-agent --namespace instana-agent --set instana.agent.key=INSTANA_AGENT_KEY --set instana.agent.endpoint.host=HOST --set instana.agent.endpoint.port=PORT --set instana.zone=K8s-cluster
```

To install the chart with the release name `instana-agent` after editing the **values.yaml** file, run:

```bash
$ helm install . --name instana-agent --namespace instana-agent
```

## Uninstalling the Chart

To uninstall/delete the `instana-agent` daemon set:

```bash
$ helm del --purge instana-agent
```

## Configuration

### Helm Chart

The following table lists the configurable parameters of the Instana chart and their default values.

|             Parameter         |            Description                                                  |                    Default                |
|-------------------------------|-------------------------------------------------------------------------|-------------------------------------------|
| `instana.agent.key`           | Your Instana Agent key                                                  | `Nil` You must provide your own key       |
| `image.name`                  | The image name to pull from                                             | `instana/agent`                           |
| `image.tag`                   | The image tag to pull                                                   | `latest`                                  |
| `image.pullPolicy`            | Image pull policy                                                       | `IfNotPresent`                            |
| `rbac.create`                 | True/False create & use RBAC resources                                  | `true`                                    |
| `instana.zone`                | Instana zone. It will be also used as cluster name and unique identifier| `k8s-cluster-name`                        |
| `instana.leaderElectorPort`   | Instana leader elector sidecar port                                     | `42655`                                   |
| `instana.agent.endpoint.host` | Instana agent backend endpoint host                                     | `saas-us-west-2.instana.io`               |
| `instana.agent.endpoint.port` | Instana agent backend endpoint port                                     | `443`                                     |
| `podAnnotations`              | Additional annotations to apply to the pod.                             | `{}`                                      |

### Agent

There is a [config map](templates/configmap.yaml) which you can edit to configure agent. This configuration will be used for all instana agents on all nodes.