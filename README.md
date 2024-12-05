---
# 🌐 Kubetally Installer Script

## 🚀 Overview

The Kubetally Installer Script is a Bash script designed to streamline the installation, upgrade, and configuration of Kubetally components in Kubernetes clusters. It leverages Helm for package management, kubectl for interacting with Kubernetes clusters, and yq for parsing YAML files. The script allows for automated validation of cluster access, installation of required binaries, and the creation of Kubernetes namespaces and resources.

---

## 📄 Kubetally Documents

- 📖 For Kubetally platform overview, please see the [Platform Overview Documentation](https://docs.avesha.io/) 🌐
- 🛠️ For the Installation guide, please see the documentation on [GitHub Repo](https://github.com/kubeslice-ent/kubetally-installer) 💻


## Getting Started

### Prerequisites

Before you begin, ensure the following steps are completed:

1. **📝 Registration:**
   - Complete the registration process at [Avesha Registration](https://avesha.io/kubetally-registration) to receive the image pull secrets required for running the script.

2. **🔧 Required Binaries:**
   - Verify that the following binaries are installed and available in your system's `PATH`:
     - **yq** 📄 (minimum version: 4.44.2)
     - **helm** 🛠️ (minimum version: 3.15.0)
     - **kubectl** ⚙️ (minimum version: 1.23.6)
     - **jq** 📦 (minimum version: 1.6.0)

3. **🌐 Kubernetes Access:**
   - Confirm that you have administrative access to the necessary Kubernetes clusters and the appropriate `kubeconfig` files are available.

---

### 🛠️ Installation Steps

1. **📂 Clone the Repository:**
   - Start by cloning the Kubetally installation Git repository:
     ```bash
     git clone https://github.com/kubeslice-ent/kubetally-installer
     ```

2. **📝 Modify the Configuration File:**
   - Navigate to the cloned repository and locate the input configuration YAML file `kubetally-installer-config.yaml`.
   - Update the following mandatory parameters:
     - **🔑 Image Pull Secrets:**
       - Insert the image pull secrets received via email as part of the registration process:
         ```yaml
         global_image_pull_secret:
           repository: "https://index.docker.io/v1/"
           username: ""  # Global Docker registry username (MANDATORY)
           password: ""  # Global Docker registry password (MANDATORY)
         ```
     - **⚙️ Kubernetes Configuration:**
       - Set the global `kubeconfig` and `kubecontext` parameters:
         ```yaml
         global_kubeconfig: ""  # Relative path to global kubeconfig file from base_path default is script directory (MANDATORY)
         global_kubecontext: ""  # Global kubecontext (MANDATORY)
         ```
     - **🐘 PostgreSQL Configuration:**
       - In-cluster PostgreSQL Service:
          - Set **skip_installation** to **false** for PostgreSQL in the additional_apps section of the configuration, and the script will automatically fetch the PostgreSQL configuration.
       - Managed PostgreSQL Service:
          - Set **skip_installation** to **true** when using a managed PostgreSQL service.
          - Provide the following PostgreSQL inputs in the configuration file:
         ``` yaml
          global:
            kubeTally:
              enabled: true
              postgresSecretName: kubetally-db-credentials
              postgresAddr:                        
              postgresPort:                            
              postgresUser:                        
              postgresPassword:                    
              postgresDB:                          
         ```

3. **🚀 Run the Installation Script:**
   - Execute the installation script using the following command:
     ```bash
     ./kubetally-installer.sh --input-yaml kubetally-installer-config.yaml
     ```

---

### Uninstallation Steps

  **Run the Cleanup Script**
   - Execute the uninstallation script using the following command:
     ```bash
     ./kubetally-uninstall.sh --input-yaml kubetally-installer-config.yaml
     ```
---

## 🛠️ Configuration details

The script requires a YAML configuration file to define various parameters and settings for the installation process. Below is an example configuration file (`kubetally-installer-config.yaml`) with descriptions for each section.

## ⚠️ Warning
**Do not copy the YAML configuration directly from this README.** Hash characters (`#`) used for comments may not be interpreted correctly. Always refer to the actual `kubetally-installer-config.yaml` file available in the repository for accurate configuration.

## YAML Configuration File

```yaml
# Base path to the root directory of your cloned repository
base_path: ""  # If left empty, the script will use the relative path to the script as the base path

# Precheck options
precheck: true  # Run general prechecks before starting the installation
kubeslice_precheck: true  # Run specific prechecks for Kubeslice components

# Global installation verification settings
verify_install: true  # Enable verification of installations globally
verify_install_timeout: 600  # Timeout for global installation verification (in seconds)
skip_on_verify_fail: false  # If set to true, skip steps where verification fails, otherwise exit on failure

# Helm repository settings
use_local_charts: true  # Use local Helm charts instead of fetching them from a repository
local_charts_path: "charts"  # Path to the directory containing local Helm charts
global_helm_repo_url: ""  # URL for the global Helm repository (if not using local charts)
global_helm_username: ""  # Username for accessing the global Helm repository
global_helm_password: ""  # Password for accessing the global Helm repository
readd_helm_repos: true  # Re-add Helm repositories even if they are already present

# List of required binaries for the installation process
required_binaries:
  - yq  # YAML processor
  - helm  # Helm package manager
  - jq  # JSON processor
  - kubectl  # Kubernetes command-line tool

# Global image pull secret settings
global_image_pull_secret:
  repository: "https://index.docker.io/v1/"  # Docker registry URL
  username: ""  # Global Docker registry username
  password: ""  # Global Docker registry password

# Node labeling settings
add_node_label: false  # Enable node labeling during installation

# Kubeconfig settings
global_kubeconfig: ""  # Relative Path to the global kubeconfig file must be in script directory (used if no specific kubeconfig is provided) - Mandatory
global_kubecontext: ""  # Global kubecontext to use - Mandatory
use_global_context: true  # If true, use the global kubecontext for all operations by default

# Enable or disable specific stages of the installation
enable_prepare_worker_values_file: true  # Prepare the worker values file for Helm charts
enable_install_controller: true  # Enable the installation of the Kubeslice controller
enable_install_ui: true  # Enable the installation of the Kubeslice UI
enable_install_worker: true  # Enable the installation of Kubeslice workers

# Kubeslice controller installation settings
kubeslice_controller:
  skip_installation: false  # Do not skip the installation of the controller
  use_global_kubeconfig: true  # Use global kubeconfig for the controller installation
  specific_use_local_charts: true  # Override to use local charts for the controller
  kubeconfig: ""  # Path to the kubeconfig file specific to the controller
  kubecontext: ""  # Kubecontext specific to the controller; if empty, uses the global context
  namespace: "kubeslice-controller"  # Kubernetes namespace where the controller will be installed
  release: "kt-controller"  # Helm release name for the controller
  chart: "kubetally-controller"  # Helm chart name for the controller
  inline_values:  # Inline Helm values for the controller chart
    global:
      imageRegistry: docker.io/aveshasystems
      kubeTally:
        enabled: true
        postgresSecretName: kubetally-db-credentials   # Default value, secret name can be overridden
        postgresAddr:                     # Optional, can be specified here or retrieved from the secret
        postgresPort:                           # Optional, can be specified here or retrieved from the secret
        postgresUser:                       # Optional, can be specified here or retrieved from the secret
        postgresPassword:                    # Optional, can be specified here or retrieved from the secret
        postgresDB:                         # Optional, can be specified here or retrieved from the secret
        postgresSslmode: require
    kubeslice:
      controller: 
        endpoint: ""  # Endpoint of the controller API server; auto-fetched if left empty
    prometheus:
      server:
        persistentVolume:       # Enable/disable Persistent Volume Claim for Prometheus data
          enabled: true         # Enable/disable Persistent Volume Claim for Prometheus data
          size: 5Gi             # Size of the PVC
      retention: "30d"          # Retention period for Prometheus data
  helm_flags: "--timeout 10m --atomic"  # Additional Helm flags for the installation
  verify_install: true  # Verify the installation of the controller
  verify_install_timeout: 30  # Timeout for the controller installation verification (in seconds)
  skip_on_verify_fail: false  # If verification fails, do not skip the step

# Kubeslice UI installation settings
kubeslice_ui:
  skip_installation: false  # Do not skip the installation of the UI
  use_global_kubeconfig: true  # Use global kubeconfig for the UI installation
  namespace: "kubeslice-controller"  # Kubernetes namespace where the UI will be installed
  release: "kt-ui"  # Helm release name for the UI
  chart: "kubetally-ui"  # Helm chart name for the UI
  inline_values:  # Inline Helm values for the UI chart
    global:
      imageRegistry: docker.io/aveshasystems
    kubeTally:
      enabled: true
    kubeslice:
      productName: kubetally
      dashboard:
        enabled: false     # Enable or disable kubernetes-dashboard, bydefault false
      uiproxy:
        service:
          type: LoadBalancer
  helm_flags: "--atomic"  # Additional Helm flags for the UI installation
  verify_install: true  # Verify the installation of the UI
  verify_install_timeout: 50  # Timeout for the UI installation verification (in seconds)
  skip_on_verify_fail: false  # If UI verification fails, do not skip the step
  specific_use_local_charts: true  # Override to use local charts for the UI

# Kubeslice worker installation settings
kubeslice_worker:
  - name: "worker-1"
    use_global_kubeconfig: true  # Use global kubeconfig for this worker
    skip_installation: false  # Do not skip the installation of the worker
    specific_use_local_charts: true  # Override to use local charts for this worker
    namespace: "kubeslice-system"  # Kubernetes namespace for this worker
    release: "kt-worker"  # Helm release name for the worker
    chart: "kubetally-worker"  # Helm chart name for the worker
    inline_values:  # Inline Helm values for the worker chart
      global:
        imageRegistry: docker.io/aveshasystems
      metrics:
        insecure: true  # Allow insecure connections for metrics
    helm_flags: "--atomic"  # Additional Helm flags for the worker installation
    verify_install: true  # Verify the installation of the worker
    verify_install_timeout: 60  # Timeout for the worker installation verification (in seconds)
    skip_on_verify_fail: false  # Do not skip if worker verification fails

# Project and cluster registration settings
enable_project_creation: true  # Enable project creation in Kubeslice
enable_cluster_registration: true  # Enable cluster registration in Kubeslice

# Define projects
projects:
  - name: "avesha"
    username: "admin"  # Username for accessing the Kubeslice project

# Define cluster registration
cluster_registration:
  - cluster_name: "worker-1"
    project_name: "avesha"
    telemetry:
      enabled: true  # Enable telemetry for this cluster
      endpoint: "http://prometheus-kube-prometheus-prometheus.kt-monitoring.svc.cluster.local:32700"  # Telemetry endpoint
      telemetryProvider: "prometheus"  # Telemetry provider (Prometheus in this case)
    geoLocation:
      cloudProvider: "GCP"  # Cloud provider for this cluster (e.g., GCP)
      cloudRegion: "us-central1"  # Cloud region for this cluster (e.g., us-central1)

# Enable or disable the installation of additional applications
enable_install_additional_apps: true  # Set to true to enable additional apps installation

# Define additional applications to install
additional_apps:
  - name: "prometheus"
    skip_installation: false  # Do not skip the installation of Prometheus
    use_global_kubeconfig: true  # Use global kubeconfig for Prometheus
    namespace: "kt-monitoring"  # Namespace where Prometheus will be installed
    release: "kt-prometheus"  # Helm release name for Prometheus
    chart: "kube-prometheus-stack"  # Helm chart name for Prometheus
    repo_url: "https://prometheus-community.github.io/helm-charts"  # Helm repository URL for Prometheus
    version: "v45.0.0"  # Version of the Prometheus stack to install
    specific_use_local_charts: true  # Use local charts for this application
    values_file: ""  # Path to an external values file, if any
    inline_values:  # Inline Helm values for Prometheus
      prometheus:
        service:
          type: NodePort
      grafana:
        enabled: false
        grafana.ini:
          auth:
            disable_login_form: true
            disable_signout_menu: true
          auth.anonymous:
            enabled: true
            org_role: Viewer
        service:
          type: LoadBalancer
        persistence:
          enabled: true
          size: 1Gi
    helm_flags: "--wait"  # Additional Helm flags for this application's installation
    verify_install: true  # Verify the installation of Prometheus
    verify_install_timeout: 600  # Timeout for verification (in seconds)
    skip_on_verify_fail: false  # Do not skip if verification fails

  - name: "postgresql"
    skip_installation: false  # Do not skip the installation of postgresql
    use_global_kubeconfig: true  # Use global kubeconfig for postgresql
    namespace: "kt-postgresql"  # Namespace where postgresql will be installed
    release: "kt-postgresql"  # Helm release name for postgresql
    chart: "postgresql"  # Helm chart name for postgresql
    repo_url: "oci://registry-1.docker.io/bitnamicharts/postgresql"  # Helm repository URL for postgresql
    version: "16.2.1"  # Version of the postgresql stack to install
    specific_use_local_charts: true  # Use local charts for this application
    values_file: ""  # Path to an external values file, if any
    inline_values:  # Inline Helm values for postgresql
      auth:
        postgresPassword: "postgres"  # Explicit password (use if not relying on `existingSecret`)
        username: "postgres"  # Explicit username (fallback if `existingSecret` is not used)
        password: "postgres"  # Password for PostgreSQL (optional)
        database: "postgres"  # Default database to create
      primary:
        persistence:
          enabled: true  # Enable persistent storage for PostgreSQL
          size: 10Gi  # Size of the Persistent Volume Claim
    helm_flags: "--wait"  # Additional Helm flags for this application's installation
    verify_install: true  # Verify the installation of postgresql
    verify_install_timeout: 600  # Timeout for verification (in seconds)
    skip_on_verify_fail: false  # Do not skip if verification fails

# Command execution settings
run_commands: false  # Enable the execution of commands defined in the YAML


```


### Explanation of YAML Fields

| **Field**                           | **Description**                                                                                                   | **Default/Example**                                                                                 |
|-------------------------------------|-------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| `base_path`                         | Base path to the root directory of the cloned repository. If empty, the script uses the relative path to the script as the base path. | `""` (empty string)                                                                                 |
| `precheck`                          | Run prechecks before installation to validate the environment and required binaries.                              | `true`                                                                                              |
| `kubeslice_precheck`                | Run specific prechecks for Kubeslice components, including cluster access validation and node label checks.       | `true`                                                                                              |
| `verify_install`                    | Enable installation verification globally, ensuring that all installed components are running as expected.        | `true`                                                                                              |
| `verify_install_timeout`            | Global timeout for verification in seconds. Determines how long the script waits for all components to be verified as running. | `600` (10 minutes)                                                                                  |
| `skip_on_verify_fail`               | Decide whether to skip further steps or exit the script if verification fails globally.                            | `false`                                                                                             |
| `global_helm_repo_url`              | URL of the global Helm repository from which charts will be pulled.                                               | <small>[Helm Repository](https://kubeslice.aveshalabs.io/repository/kubetally-helm-ent-prod/)</small>                     |
| `global_helm_username`              | Username for accessing the global Helm repository, if required.                                                   | `""`                                                                                                |
| `global_helm_password`              | Password for accessing the global Helm repository, if required.                                                   | `""`                                                                                                |
| `readd_helm_repos`                  | Re-add Helm repositories if they already exist to ensure the latest repository configuration is used.             | `true`                                                                                              |
| `required_binaries`                 | List of binaries that are required for the installation process. The script will check for these binaries and exit if any are missing. | `yq`, `helm`, `kubectl`                                                                             |
| `global_image_pull_secret`          | Global image pull secret settings for accessing private Docker registries.                                         | Repository: `https://index.docker.io/v1/`, Username: `""`, Password: `""`                           |
| `add_node_label`                    | Enable node labeling during installation, useful for reserving nodes for specific tasks.                           | `true`                                                                                              |
| `global_kubeconfig`                 | Relative path to the global kubeconfig file used to access Kubernetes clusters.                                    | `""` (empty string)                                                                                 |
| `use_local_charts`                  | Use local Helm charts instead of pulling them from a repository, useful for testing or restricted access scenarios. | `true`                                                                                              |
| `local_charts_path`                 | Relative path to the local Helm charts directory, used only if `use_local_charts` is set to `true`.               | `"charts"`                                                                                          |
| `global_kubecontext`                | Global kubecontext to be used across all Kubernetes interactions. If empty, the default context will be used.     | `""` (empty string)                                                                                 |
| `use_global_context`                | Use the global kubecontext by default for all operations unless a specific context is provided for a component.   | `true`                                                                                              |
| `enable_prepare_worker_values_file` | Enable the preparation of the worker values file before installation, necessary if the worker configuration depends on dynamic values. | `true`                                                                                              |
| `enable_install_controller`         | Enable the installation of the Kubeslice controller.                                                               | `true`                                                                                              |
| `enable_install_ui`                 | Enable the installation of the Kubeslice UI.                                                                       | `true`                                                                                              |
| `enable_install_worker`             | Enable the installation of the Kubeslice worker.                                                                   | `true`                                                                                              |

#### `kubeslice_controller` Subfields

| **Subfield**                  | **Description**                                                                                         | **Default/Example**                                                             |
|-------------------------------|---------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `skip_installation`           | Skip the installation of the Kubeslice controller if it's already installed or not needed.               | `false`                                                                         |
| `use_global_kubeconfig`       | Use the global kubeconfig file for the controller installation.                                          | `true`                                                                          |
| `specific_use_local_charts`   | Use local charts specifically for the controller installation, overriding the global `use_local_charts` setting. | `true`                                                                          |
| `kubeconfig`                  | Relative path to the kubeconfig file for the controller. Overrides the global kubeconfig if specified.    | `""` (empty string)                                                             |
| `kubecontext`                 | Specific kubecontext for the controller installation, uses the global context if empty.                  | `""` (empty string)                                                             |
| `namespace`                   | Kubernetes namespace where the Kubeslice controller will be installed.                                   | `"kubeslice-controller"`                                                        |
| `release`                     | Helm release name for the Kubeslice controller.                                                          | `"kubeslice-controller-release"`                                                |
| `chart`                       | Helm chart name used for installing the Kubeslice controller.                                            | `"kubeslice-controller"`                                                    |
| `inline_values`               | Inline values passed to the Helm chart during installation. For example, setting the controller endpoint. | `kubeslice.controller.endpoint: ""`                                             |
| `helm_flags`                  | Additional Helm flags for the controller installation, such as timeout and atomic deployment.            | `"--timeout 10m --atomic"`                                                      |
| `verify_install`              | Verify the installation of the Kubeslice controller after deployment.                                    | `true`                                                                          |
| `verify_install_timeout`      | Timeout for verifying the installation of the controller, in seconds.                                    | `30` (30 seconds)                                                               |
| `skip_on_verify_fail`         | Skip further steps or exit if the controller verification fails.                                         | `false`                                                                         |

#### `kubeslice_ui` Subfields

| **Subfield**                | **Description**                                                                                           | **Default/Example**                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| `skip_installation`         | Skip the installation of the Kubeslice UI if it's already installed or not needed.                         | `false`                                                       |
| `use_global_kubeconfig`     | Use the global kubeconfig file for the UI installation.                                                   | `true`                                                        |
| `specific_use_local_charts` | Use local charts specifically for the UI installation, overriding the global `use_local_charts` setting.  | `true`                                                        |
| `namespace`                 | Kubernetes namespace where the Kubeslice UI will be installed.                                            | `"kubeslice-controller"`                                      |
| `release`                   | Helm release name for the Kubeslice UI.                                                                   | `"kubeslice-ui"`                                              |
| `chart`                     | Helm chart name used for installing the Kubeslice UI.                                                     | `"kubeslice-ui"`                                          |
| `helm_flags`                | Additional Helm flags for the UI installation, such as atomic deployment.                                 | `"--atomic"`                                                  |
| `verify_install`            | Verify the installation of the Kubeslice UI after deployment.                                             | `true`                                                        |
| `verify_install_timeout`    | Timeout for verifying the installation of the UI, in seconds.                                             | `50` (50 seconds)                                             |
| `skip_on_verify_fail`       | Skip further steps or exit if the UI verification fails.                                                  | `false`                                                       |

#### `kubeslice_worker` Subfields

| **Subfield**                | **Description**                                                                                         | **Default/Example**                                                                                         |
|-----------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| `name`                      | Name of the worker node configuration.                                                                  | `"worker-1"`                                                                                                |
| `use_global_kubeconfig`     | Use the global kubeconfig file for the worker installation.                                             | `true`                                                                                                      |
| `skip_installation`         | Skip the installation of the worker if it's already installed or not needed.                            | `false`                                                                                                     |
| `specific_use_local_charts` | Use local charts specifically for the worker installation, overriding the global `use_local_charts` setting. | `true`                                                                                                      |
| `namespace`                 | Kubernetes namespace where the worker will be installed.                                                | `"kubeslice-system"`                                                                                        |
| `release`                   | Helm release name for the worker.                                                                       | `"kubeslice-worker1-release"`                                                                               |
| `chart`                     | Helm chart name used for installing the worker.                                                         | `"kubeslice-worker"`                                                                                    |
| `inline_values`             | Inline values passed to the Helm chart during installation. For example, disabling networking and setting Prometheus endpoints. | `kubesliceNetworking.enabled: false`, `metrics.insecure: true` |
| `helm_flags`                | Additional Helm flags for the worker installation, such as atomic deployment.                           | `"--atomic"`                                                                                                |
| `verify_install`            | Verify the installation of the worker after deployment.                                                 | `true`                                                                                                      |
| `verify_install_timeout`    | Timeout for verifying the installation of the worker, in seconds.                                       | `60` (60 seconds)                                                                                           |
| `skip_on_verify_fail`       | Skip further steps or exit if the worker verification fails.                                            | `false`                                                                                                     |
#### `Custom App Manifests` Subfields

| **Field**                       | **Description**                                                                                                         | **Type**          | **Required** | **Example**                                                                                                         |
|---------------------------------|-------------------------------------------------------------------------------------------------------------------------|-------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| `manifests`                     | A list of manifest configurations. Each entry defines how a specific Kubernetes manifest should be applied.            | `list`            | Yes          | See below for individual fields.                                                                                    |
| `manifests[].appname`           | The name of the application or resource. Used for logging and identification purposes.                                   | `string`          | Yes          | `nginx-deployment`                                                                                                  |
| `manifests[].manifest`          | The path to the Kubernetes manifest file. Can be a local file or an HTTPS URL.                                          | `string`          | No           | `nginx/deployment.yaml` or `https://raw.githubusercontent.com/.../nginx-app.yaml`                                   |
| `manifests[].overrides_yaml`    | The path to a YAML file containing overrides for the base manifest. Merges with the base manifest before applying.      | `string`          | No           | `nginx/overrides.yaml`                                                                                              |
| `manifests[].inline_yaml`       | Inline YAML content to be merged with the base manifest. Allows for quick, in-line customization without separate files. | `string` (YAML)   | No           | See inline YAML example below.                                                                                      |
| `manifests[].use_global_kubeconfig` | Determines whether the global kubeconfig and context should be used. If `false`, specific kubeconfig and context must be provided. | `boolean`        | Yes          | `true`                                                                                                              |
| `manifests[].kubeconfig_path`   | Path to a specific Kubernetes configuration file to be used instead of the global kubeconfig.                           | `string`          | No           | `/path/to/specific/kubeconfig`                                                                                      |
| `manifests[].kubecontext`       | The context name in the specific Kubernetes configuration file to be used for this manifest.                            | `string`          | No           | `specific-context`                                                                                                  |
| `manifests[].skip_installation` | Whether to skip the installation of this manifest. Useful for conditional deployments.                                  | `boolean`         | Yes          | `false`                                                                                                             |
| `manifests[].verify_install`    | Whether to verify that the application or resource was successfully deployed.                                           | `boolean`         | Yes          | `true`                                                                                                              |
| `manifests[].verify_install_timeout` | The timeout in seconds for the installation verification.                                                        | `integer`         | Yes          | `60`                                                                                                                |
| `manifests[].skip_on_verify_fail` | Whether to skip the remaining operations if the verification fails.                                                  | `boolean`         | Yes          | `false`                                                                                                             |
| `manifests[].namespace`         | The Kubernetes namespace where the resources should be applied.                                                        | `string`          | Yes          | `nginx-namespace`                                                                                                   |


```
### 🧑‍💻 Script Usage
To run the script, use the following command:
```
```bash
./kubtally-installer.sh --input-yaml <yaml_file>
```


Replace `<yaml_file>` with the path to your YAML configuration file. For example:

```bash
./kubtally-installer.sh --input-yaml kubtally-installer-config.yaml
```

### 💡 Command-Line Options

- `--input-yaml <yaml_file>`: Specifies the YAML configuration file to be used.
- `--help`: Displays usage information.


---


## 📋 Usage

## 🔹 Parameters

- **`-k, --kubeconfig`** (required): Absolute path to the kubeconfig file used to connect to the Kubernetes cluster.
- **`-s, --slice`** (optional if `-a` is provided): Name of the slice for which the tokens are to be retrieved.
- **`-p, --project`** (required): Name of the project (namespace) where the slice is located.
- **`-a, --admin`** (optional): Fetch admin tokens for specified usernames (makes `--slice` optional).
- **`-u, --username`** (required with `-a`): Comma-separated list of usernames for fetching admin tokens.
- **`-h, --help`**: Display help message.

## 🚀 Examples


- **Explanation**: This command fetches tokens for the slice `pool1` in the namespace `kubeslice-avesha`.
- **Parameters**:
  - `-k /path/to/kubeconfig`: Specifies the path to the kubeconfig file.
  - `-s pool1`: Specifies the slice name (`pool1`).
  - `-p avesha`: Specifies the project/namespace name (`avesha`).

---




### 🔑 Key Features

1. **Prerequisite Checks**: Ensures that required binaries are installed. 🛠️
2. **Kubeslice Pre-Checks**: Validates access to clusters and labels nodes if required. ✅
3. **Helm Chart Management**: Adds, updates, or removes Helm repositories and manages chart installations. 📦
4. **Project and Cluster Management**: Automates the creation of projects and registration of clusters in the Kubeslice controller. 🗂️
5. **Worker Configuration**: Fetches secrets from the controller cluster, prepares worker-specific values files, and manages worker installations. ⚙️

### 📜 Example Workflow

1. **Run Pre-checks**: The script first validates that all prerequisites are met. ✅
2. **Kubeslice Pre-Checks**: Validates that the script can access all necessary clusters. 🔍
3. **Install or Upgrade Helm Charts**:
   - Installs or upgrades the Kubeslice controller. 📦
   - Installs or upgrades the Kubeslice UI. 💻
4. **Project and Cluster Management**:
   - Creates defined projects in the Kubeslice controller. 🗂️
   - Registers defined clusters within these projects. 🌍
5. **Worker Installation**: Installs or upgrades worker nodes, applying the necessary configuration. ⚙️

### 📝 Notes

- Ensure the YAML configuration file is correctly formatted and contains all necessary fields. 📄
- The script will exit with an error if any critical steps fail unless configured to skip on failure. ❌
- Paths specified in the YAML file should be relative to the `base_path` unless absolute paths are used. 📁

### 🛠️ Troubleshooting

- **Missing Binaries**: Ensure all required binaries are installed and accessible in your system's `PATH`. ⚠️
- **Cluster Access Issues**: Verify that kubeconfig files are correctly configured and that the script can access the clusters specified in the YAML configuration. 🔧
- **Timeouts**: If a component fails to install within the specified timeout, increase the `verify_install_timeout` in the YAML file. ⏳

---
