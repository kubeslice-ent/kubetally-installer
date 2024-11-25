# kubetally Enterprise Worker Helm Charts



The Slice Operator, also known as a kubetally Worker is a Kubernetes Operator component that manages the life-cycle of the kubetally related Custom Resource Definitions (CRDs). Please refer to the [documentation](https://docs.avesha.io/documentation/enterprise/1.6.0/architecture) for more details.

# Usage

[Helm](https://helm.sh) must be installed to use the charts.
Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add the repo as follows:

```console
helm repo add kubetally https://kubetally.aveshalabs.io/repository/kubetally-helm-ent-prod/ 
```

You can then run `helm search repo kubetally` to see the charts.

## Setting Up kubetally Worker

### Install Chart

For setting up Worker Operator on cloud platforms, refer to the [kubetally Worker Installation on Cloud](https://docs.avesha.io/documentation/enterprise/1.6.0/install-kubetally/yaml/yaml-controller-install).

### Uninstall Chart

Please refer the following kubetally Worker [uninstallation](https://docs.avesha.io/documentation/enterprise/1.6.0/uninstall-kubetally/) steps.

### Upgrading Chart

```console
helm upgrade [RELEASE_NAME] kubetally/kubetally-worker [flags]
```

Guide
---
A full and comprehensive documentation is available on our enterprise [documentation](https://docs.avesha.io/documentation/enterprise/) website.
