# kubetally Manager Helm Charts


kubetally Manager is a UI application to register clusters and manage application slices. It connects to the kubetally Controller API gateway to access data. Users can log in using a service account to manage the slices. Please refer to the [documentation](https://docs.avesha.io/documentation/enterprise/1.6.0/architecture) for more details.

# Usage

[Helm](https://helm.sh) must be installed to use the charts.
Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add the repo as follows:

```console
helm repo add kubetally https://kubetally.aveshalabs.io/repository/kubetally-helm-ent-prod/ 
```

You can then run `helm search repo kubetally` to see the charts.

## Setting Up kubetally Manager

### Install Chart
You must install the kubetally Controller chart before installing the kubetally Manager chart. 

For setting up kubetally Manager on cloud platforms, refer to the [kubetally Manager Installation on Cloud](https://docs.avesha.io/documentation/enterprise/1.6.0/install-kubetally/yaml/yaml-controller-install#install-the-kubetally-manager).

### Uninstall Chart

Please refer the following kubetally Manager [uninstallation](https://docs.avesha.io/documentation/enterprise/1.6.0/uninstall-kubetally/) steps.

### Upgrading Chart

```console
helm upgrade [RELEASE_NAME] kubetally/kubetally-ui [flags]
```

Guide
---
A full and comprehensive documentation is available on our enterprise [documentation](https://docs.avesha.io/documentation/enterprise/) website.
