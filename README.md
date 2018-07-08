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

## CPU and Memory Resources
The Helm chart comes with support for configured resource requests and limits.
By default these values are commented out. 
It is __highly__ recommended to change this behavior on a production deployment. Also the Helm Chart provides a way to control Jenkins Java Memory Opts. When using Jenkins in production, you will need to set the values that suites your needs.

## Persistence
By default the helm chart allocates a 20gb volume for jenkins storage.
The chart provides the ability to control:
* `persistence.jenkinsHome.enabled` - if set to false, jenkins home will be using empty{} volume instead of persistentVolumeClaim. Default is `true`
* `persistence.jenkinsHome.size` - the managed volume size
* `persistence.jenkinsHome.storageClass` - If set to "-", storageClass: "", which disables dynamic provisioning. If undefined (the default) or set to null, no storageClass spec is set, choosing the default provisioner. (gp2 on AWS, standard on GKE, AWS & OpenStack)
* `persistence.jenkinsHome.existingClaim` - if provided, jenkins storage will be stored on an manually managed persistentVolumeClaim 
* `persistence.jenkinsHome.annotations` - annotations that will be added to the managed persistentVolumeClaim


 




