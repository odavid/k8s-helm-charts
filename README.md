# k8s Helm Charts
[![Build Status](https://travis-ci.org/odavid/k8s-helm-charts.svg?branch=master)](https://travis-ci.org/odavid/k8s-helm-charts)

List of Helm Charts to support docker images that are maintained by [odavid](https://github.com/odavid).

## Adding Chart Repository to Helm
In order to use this repo as `odavid`, run the following command:

```shell
helm repo add odavid https://odavid.github.io/k8s-helm-charts
```


## Charts

Name      | Description
----------|------------
[my-bloody-jenkins](charts/my-bloody-jenkins)| Deploy [My Bloody Jenkins](https://github.com/odavid/my-bloody-jenkins) in kubernetes cluster