# kubetally Controller Helm Charts


The kubetally Controller is installed in one of the clusters and provides a central configuration management system, for slices across multiple clusters. Please refer to the [documentation](https://docs.avesha.io/documentation/enterprise/1.6.0/architecture) for more details.

# Usage

[Helm](https://helm.sh) must be installed to use the charts.
Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add the repo as follows:

```console
helm repo add kubetally https://kubetally.aveshalabs.io/repository/kubetally-helm-ent-prod/ 
```

You can then run `helm search repo kubetally` to see the charts.

## Setting Up kubetally Controller

### Install Chart

For setting up kubetally Controller on cloud platforms, refer to the [kubetally Controller Installation on Cloud](https://docs.avesha.io/documentation/enterprise/1.6.0/install-kubetally/yaml/yaml-controller-install).

### Uninstall Chart

Please refer the following kubetally Controller [uninstallation](https://docs.avesha.io/documentation/enterprise/1.6.0/uninstall-kubetally/) steps.

### Upgrading Chart

```console
helm upgrade [RELEASE_NAME] kubetally/kubetally-controller [flags]
```

Guide
---
A full and comprehensive documentation is available on our enterprise [documentation](https://docs.avesha.io/documentation/enterprise/) website.
