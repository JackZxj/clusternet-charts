# How to install clusternet-agent

`clusternet-agent` is responsible for

- auto-registering current cluster to a parent cluster as a child cluster, which is also been called `ManagedCluster`;
- reporting heartbeats of current cluster, including Kubernetes version, running platform, `healthz`/`readyz`/`livez`
  status, etc;
- setting up a websocket connection that provides full-duplex communication channels over a single TCP connection to
  parent cluster;

## Prerequisites

- Kubernetes 1.18+
- Helm 3.1.0

## TL;DR

```bash
helm repo add clusternet https://clusternet.github.io/charts
helm install clusternet-agent -n clusternet-system --create-namespace \
  --set parentURL=PLEASE-CHANGE-ME \
  --set registrationToken=PLEASE-CHANGE-ME \
  clusternet/clusternet-agent
```

Please update `PLEASE-CHANGE-ME` to your valid configurations, such as,

```bash
helm install clusternet-agent -n clusternet-system --create-namespace \
  --set parentURL=https://192.168.10.10:6443 \
  --set registrationToken=07401b.f395accd246ae52d \
  clusternet/clusternet-agent
```

**Note:** If bootstrapping authentication is **not supported** by the kube-apiserver in parent cluster (like k3s),
i.e. `--enable-bootstrap-token-auth=false` (which defaults to be false), please use serviceaccount token instead.

[Click here to get the serviceaccount token from parent cluster.](../clusternet-hub/README.md#get-serviceaccount-token-from-parent-cluster)

```bash
helm install clusternet-agent -n clusternet-system --create-namespace \
  --set parentURL=https://192.168.10.10:6443 \
  --set registrationToken=Parent-Cluster-ServiceAccount-Token \
  clusternet/clusternet-agent
```

## Step-by-step Installation & Uninstallation

### Installing the Chart

> Note:
> The images are synced to [dockerhub](https://hub.docker.com/u/clusternet) as well,
> you could set `image.registry` to empty or `docker.io` if needed.

To install the chart with the release name `clusternet-agent` and release namespace `clusternet-system`:

```bash
helm repo add clusternet https://clusternet.github.io/charts
helm install clusternet-agent -n clusternet-system --create-namespace \
  --set parentURL=PLEASE-CHANGE-ME \
  --set registrationToken=PLEASE-CHANGE-ME \
  clusternet/clusternet-agent
```

Please update `PLEASE-CHANGE-ME` to your valid configurations, such as,

```bash
helm install clusternet-agent -n clusternet-system --create-namespace \
  --set parentURL=https://192.168.10.10:6443 \
  --set registrationToken=07401b.f395accd246ae52d \
  clusternet/clusternet-agent
```

These commands deploy `clusternet-agent` on the Kubernetes cluster in the default configuration.
The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list -A`

### Uninstalling the Chart

To uninstall/delete the `clusternet-agent` deployment:

```bash
helm delete clusternet-agent -n clusternet-system
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

### Parameters

#### Common parameters

| Name                | Description                                        | Value |
| ------------------- | -------------------------------------------------- | ----- |
| `kubeVersion`       | Override Kubernetes version                        | `""`  |
| `nameOverride`      | String to partially override common.names.fullname | `""`  |
| `fullnameOverride`  | String to fully override common.names.fullname     | `""`  |
| `commonLabels`      | Labels to add to all deployed objects              | `{}`  |
| `commonAnnotations` | Annotations to add to all deployed objects         | `{}`  |

#### Exposure parameters

| Name                              | Description                                                                               | Value                                                               |
| --------------------------------- | ----------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| `parentURL`                       | The apiserver address of parent cluster                                                   | `""`                                                                |
| `registrationToken`               | The bootstrap token used by cluster registration                                          | `""`                                                                |
| `replicaCount`                    | Specify number of clusternet-agent replicas                                               | `3`                                                                 |
| `serviceAccount.name`             | The name of the ServiceAccount to create                                                  | `"clusternet-agent"`                                                |
| `image.registry`                  | clusternet-agent image registry                                                           | `ghcr.io`                                                           |
| `image.repository`                | clusternet-agent image repository                                                         | `clusternet/clusternet-agent`                                       |
| `image.tag`                       | clusternet-agent image tag (immutable tags are recommended)                               | `v0.11.0`                                                           |
| `image.pullPolicy`                | clusternet-agent image pull policy                                                        | `IfNotPresent`                                                      |
| `image.pullSecrets`               | Specify docker-registry secret names as an array                                          | `[]`                                                                |
| `extraArgs.feature-gates`         | Feature gates are a set of key=value pairs that describe clusternet-agent features.       | `SocketConnection=true,AppPusher=true,Recovery=true,Predictor=true` |
| `extraArgs.cluster-sync-mode`     | Specify the sync mode 'Pull', 'Push' and 'Dual' between parent cluster and child cluster  | `Dual`                                                              |
| `extraArgs.use-metrics-server`    | Use metrics server                                                                        | `false`                                                             |
| `extraArgs.cluster-reg-name`      | Specify the cluster registration name                                                     | `mycluster`                                                         |
| `extraArgs.cluster-reg-namespace` | Specify the cluster registration namespace                                                | `mycluster-ns`                                                      |
| `resources.limits`                | The resources limits for the container                                                    | `{}`                                                                |
| `resources.requests`              | The requested resources for the container                                                 | `{}`                                                                |
| `nodeSelector`                    | Node labels for pod assignment                                                            | `{}`                                                                |
| `priorityClassName`               | Set Priority Class Name to allow priority control over other pods                         | `""`                                                                |
| `hostNetwork`                     | Specify whether to use the host's networking stack                                        | `false`                                                             |
| `tolerations`                     | Tolerations for pod assignment                                                            | `[]`                                                                |
| `podAffinityPreset`               | Pod affinity preset. Ignored if `affinity` is set. Allowed values: `soft` or `hard`       | `""`                                                                |
| `podAntiAffinityPreset`           | Pod anti-affinity preset. Ignored if `affinity` is set. Allowed values: `soft` or `hard`  | `soft`                                                              |
| `nodeAffinityPreset.type`         | Node affinity preset type. Ignored if `affinity` is set. Allowed values: `soft` or `hard` | `""`                                                                |
| `nodeAffinityPreset.key`          | Node label key to match. Ignored if `affinity` is set.                                    | `""`                                                                |
| `nodeAffinityPreset.values`       | Node label values to match. Ignored if `affinity` is set.                                 | `[]`                                                                |
| `affinity`                        | Affinity for pod assignment                                                               | `{}`                                                                |
