# Helm Charts
![main](https://github.com/odavid/k8s-helm-charts/workflows/main/badge.svg)

List of Helm Charts to support docker images that are maintained by [odavid](https://github.com/odavid).

## Adding Chart Repository to Helm
In order to use this repo as `odavid`, run the following command:

```shell
helm repo add odavid https://odavid.github.io/k8s-helm-charts
```


## Charts

Name|Description|Deploy Script
---|---|--------------
[my-bloody-jenkins](charts/my-bloody-jenkins)| [My Bloody Jenkins](https://github.com/odavid/my-bloody-jenkins) in k8s cluster| ```helm install odavid/my-bloody-jenkins [-f values.yml]```
