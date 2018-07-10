# My Bloody Jenkins Helm Chart

## Prerequisites Details
* Kubernetes 1.8+

## Chart Details
The chart will do the following:
* Deploy [My Bloody Jenkins](https://github.com/odavid/my-bloody-jenkins)
* Manage Configuration in a dedicated ConfigMap
* Configures Jenkins to use a default [k8s jenkins cloud](https://plugins.jenkins.io/kubernetes)
* Optionally expose Jenkins with [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
* Manages a [Persistent Volume Claim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) for Jenkins Storage
* Optionally mount extenral [secrets](https://kubernetes.io/docs/concepts/configuration/secret/) as volumes to be used within the configuration [See docs](https://github.com/odavid/my-bloody-jenkins/pull/102)
* Optionally mount extenral [configMaps](https://kubernetes-v1-4.github.io/docs/user-guide/configmap/) to be used as configuration data sources [See docs](https://github.com/odavid/my-bloody-jenkins/pull/102)
* Optionally configures [rbac](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) and a dedicated [service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)


## Installing the Chart
To install the chart with the release name `jenkins`:
```shell
helm install --name jenkins charts/my-bloody-jenkins
```

To install the chart with a custom configuration values.yml
```shell
helm install --name jenkins charts/my-bloody-jenkins -f <valueFiles>
```

## Upgrading the Release
To install the chart with a custom configuration values.yml
```shell
helm upgrade jenkins charts/my-bloody-jenkins -f <valueFiles>
```

## Deleting the Chart
```shell
helm delete jenkins
```

## Docker Image
By default the chart uses the [`odavid/my-bloody-jenkins:lts`](https://hub.docker.com/r/odavid/my-bloody-jenkins/tags/) image.
The Helm Chart provides a way to use different repo or tags:
* `image.repository` - by default `odavid/my-bloody-jenkins`
* `image.tag` - by default `lts`
* `image.pullPolicy` - by default `IfNotPresent`
* `image.imagePullSecret` - not set by default


## CPU and Memory Resources
The Helm chart comes with support for configured resource requests and limits.
By default these values are commented out.
It is __highly__ recommended to change this behavior on a production deployment. Also the Helm Chart provides a way to control Jenkins Java Memory Opts. When using Jenkins in production, you will need to set the values that suites your needs.

## Persistence
By default the helm chart allocates a 20gb volume for jenkins storage.
The chart provides the ability to control:
* `persistence.jenkinsHome.enabled` - if set to false, jenkins home will be using empty{} volume instead of persistentVolumeClaim. Default is `true`
* `persistence.jenkinsHome.size` - the managed volume size
* `persistence.jenkinsHome.storageClass` - If set to `"-"`, then storageClass: `""`, which disables dynamic provisioning. If undefined (the default) or set to null, no storageClass spec is set, choosing the default provisioner. (gp2 on AWS, standard on GKE, AWS & OpenStack)
* `persistence.jenkinsHome.existingClaim` - if provided, jenkins storage will be stored on an manually managed persistentVolumeClaim
* `persistence.jenkinsHome.annotations` - annotations that will be added to the managed persistentVolumeClaim

## Secrets
My Bloody Jenkins natively supports [environment variable substitution](https://github.com/odavid/my-bloody-jenkins#environment-variable-substitution-and-remove-master-env-vars) within its configuration files.
The Helm Chart provides a simple way to map [k8s secrets] in dedicated folders that will be later on used as environment variables datasource.

In order to use this feature, you will need to create external secrets and then use: `envSecrets` property to add these secrets to the search order.
For example:
```shell
echo -n 'admin' > ./username
echo -n 'password' > ./password
kubectl create secret generic my-jenkins-secret --from-file=./username --from-file=./password
```

Then add this secret to values.yml:
```yaml
envSecrets:
    - my-jenkins-secret
```
Now, you can refer these secrets as environmnet variables:
* `MY_JENKINS_SECRET_USERNAME`
* `MY_JENKINS_SECRET_PASSWORD`

See [Support multiple data sources and secrets from files](https://github.com/odavid/my-bloody-jenkins/pull/102) for more details

## Managed Configuration and additional ConfigMaps
My Bloody Jenkins natively supports watching multiple config data sources and merge them into one config top to bottom
The Helm Chart provides a way to define a `managedConfig` yaml within the chart values.yml as well as add additional external `configMaps` that will be merged/override the default configuration.

See [Support multiple data sources and secrets from files](https://github.com/odavid/my-bloody-jenkins/pull/102) for more details
The `managedConfig` is mounted as `/var/jenkins_managed_config/jenkins-config.yml` and contains the `managedConfig` yaml contents

Additional `configMaps` list are mounted as `/var/jenkins_config/<ConfigMapName>` within the container and are merged with the `managedConfig`


## Configuration

The following table lists the configurable parameters of the chart and their default values.

|         Parameter         |           Description             |                         Default                          |
|---------------------------|-----------------------------------|----------------------------------------------------------|
| `image.repository`        | `My Bloody Jenkins` Docker Image       | `odavid/my-bloody-jenkins`
| `image.tag`               | `My Bloody Jenkins` Docker Image Tag       | `lts`
| `image.pullPolicy`        | Image Pull Policy                 | `IfNotPresent`
| `image.imagePullSecrets`        | Docker registry pull secret       |
| `service.type`            | Service Type   | `LoadBalanacer`
| `service.annotations`        | Service Annotations       | `{}`
| `service.loadBalancerSourceRanges`        | Array Of IP CIDR ranges to whitelist (Only if service type is `LoadBalancer`) |
| `service.loadBalancerIP`        | Service Load Balancer IP Address (Only if service type is `LoadBalancer`) |
| `ingress.enabled`        | If `true` Ingress will be created      | `false`
| `ingress.path`        | Ingress Path (Only if ingress is enabled)| `/`
| `ingress.annotations`        | Ingress Annoations| `{}`
| `ingress.hostname`        | Ingress Hostname (Required only if ingress is enabled)|
| `ingress.tls.secretName`        | Ingress TLS Secret Name - if provided, the ingress will terminate TLS|
| `rbac.create`        | If `true` - a ServiceAccount, and a Role will be created| `false`
| `rbac.clusterWideAccess`        | If `true` - A ClusterRole will be created instead of Role - relevant only if `rbac.create` is `true`| `false`
| `resources.requests.cpu` | Initial CPU Request  |
| `resources.requests.memory` | Initial Memory Request  |
| `resources.limits.cpu` | CPU Limit |
| `resources.limits.memory` | Memory Limit |
| `readinessProbe.timeout` | Readiness Probe Timeout | `5`
| `readinessProbe.initialDelaySeconds` | Readiness Probe Initial Delay in seconds | `5`
| `readinessProbe.periodSeconds` | Readiness Probe - check for readiess every `X` seconds | `5`
| `readinessProbe.failureThreshold` | Readiness Probe - Mark the pod as not ready for traffic after `X` consecutive failures | `3`
| `livenessProbe.timeout` | Liveness Probe Timeout | `5`
| `livenessProbe.initialDelaySeconds` | Liveness Probe Initial Delay in seconds - a high value since it takes time to start| `600`
| `livenessProbe.periodSeconds` | Liveness  Probe - check for liveness every `X` seconds | `5`
| `livenessProbe.failureThreshold` | Liveness Probe - Kill the pod after `X` consecutive failures | `3`
| `persistence.mountDockerSocket` | If `true` - `/var/run/docker.sock` will be mounted | `true`
| `persistence.jenkinsHome.enabled` | If `true` - Jenkins Storage will be persistent | `true`
| `persistence.jenkinsHome.existingClaim` | External Jenkins Storage PesistentVolumeClaim - if set, then no volume claim will be created by the Helm Chart|
| `persistence.jenkinsHome.annotations` | Jenkins Storage PesistentVolumeClaim annotations | `{}`
| `persistence.jenkinsHome.accessMode` | Jenkins Storage PesistentVolumeClaim accessMode | `ReadWriteOnce`
| `persistence.jenkinsHome.size` | Jenkins Storage PesistentVolumeClaim size | `20Gi`
| `persistence.jenkinsHome.storageClass` | External Jenkins Storage PesistentVolumeClaim | If set to `"-"`, then storageClass: `""`, which disables dynamic provisioning. If undefined (the default) or set to null, no storageClass spec is set, choosing the default provisioner. (gp2 on AWS, standard on GKE, AWS & OpenStack)
| `persistence.jenkinsWorkspace.enabled` | If `true` - Jenkins Workspace Storage will be persistent | `false`
| `persistence.jenkinsWorkspace.existingClaim` | External Jenkins Workspace Storage PesistentVolumeClaim - if set, then no volume claim will be created by the Helm Chart|
| `persistence.jenkinsWorkspace.annotations` | Jenkins Workspace Storage PesistentVolumeClaim annotations | `{}`
| `persistence.jenkinsWorkspace.accessMode` | Jenkins Workspace Storage PesistentVolumeClaim accessMode | `ReadWriteOnce`
| `persistence.jenkinsWorkspace.size` | Jenkins Workspace Storage PesistentVolumeClaim size | `8Gi`
| `persistence.jenkinsWorkspace.storageClass` | External Jenkins Workspace Storage PesistentVolumeClaim | If set to `"-"`, then storageClass: `""`, which disables dynamic provisioning. If undefined (the default) or set to null, no storageClass spec is set, choosing the default provisioner. (gp2 on AWS, standard on GKE, AWS & OpenStack)
| `persistence.volumes` | Additional volumes to be included within the Deployments |
| `persistence.mounts` | Additional mounts to be mounted to the container |
| `nodeSelector` | Node Selector | `{}`
| `tolerations` | Tolerations | `[]`
| `affinity` | Affinity | `{}`
| `env` | Additional Environment Variables to be passed to the container - format `key`: `value` |
| `envSecrets` | List of external secret names to be mounted as env secrets - see [Docs](https://github.com/odavid/my-bloody-jenkins/pull/102) |
| `configMaps` | List of external config maps to be used as configuration files - see [Docs](https://github.com/odavid/my-bloody-jenkins/pull/102) |
| `jenkinsAdminUser` | The name of the admin user - must be a valid user within the [Jenkins Security Realm](https://github.com/odavid/my-bloody-jenkins#security-section)| `admin`
| `javaMemoryOpts` | Jenkins Java Memory Opts | `-Xmx256m`
| `managedConfig` | `My Bloody Jenkins` Configuration yaml - See [Configuration Reference](https://github.com/odavid/my-bloody-jenkins#configuration-reference) | By default, a k8s cloud with default node label `generic` is created, enabling Jenkins to provision jnlp slave nodes within the cluster
