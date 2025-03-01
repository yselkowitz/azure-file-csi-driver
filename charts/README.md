# Install CSI driver with Helm 3

## Prerequisites
 - [install Helm](https://helm.sh/docs/intro/quickstart/#install-helm)

### Tips
 - make controller only run on master node: `--set controller.runOnMaster=true`
 - enable `fsGroupPolicy` on a k8s 1.20+ cluster: `--set feature.enableFSGroupPolicy=true`
 - set replica of controller as `1`: `--set controller.replicas=1` (only applied for NFS protocol)
 - specify different cloud config secret for the driver:
   - `--set controller.cloudConfigSecretName`
   - `--set controller.cloudConfigSecretNamesapce`
   - `--set node.cloudConfigSecretName`
   - `--set node.cloudConfigSecretNamesapce`
 - switch to `mcr.azk8s.cn` repository in Azure China: `--set image.baseRepo=mcr.azk8s.cn`

### install a specific version
```console
helm repo add azurefile-csi-driver https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/charts
helm install azurefile-csi-driver azurefile-csi-driver/azurefile-csi-driver --namespace kube-system --version v1.8.0
```

### install on RedHat/CentOS
```console
helm install azurefile-csi-driver azurefile-csi-driver/azurefile-csi-driver --namespace kube-system --set linux.distro=fedora
```

### install driver with customized driver name, deployment name
> only supported from `v1.5.0`+
 - following example would install a driver with name `file2`
```console
helm install azurefile2-csi-driver azurefile-csi-driver/azurefile-csi-driver --namespace kube-system --set driver.name="file2.csi.azure.com" --set controller.name="csi-azurefile2-controller" --set rbac.name=azurefile2 --set serviceAccount.controller=csi-azurefile2-controller-sa --set serviceAccount.node=csi-azurefile2-node-sa --set linux.dsName=csi-azurefile2-node --set windows.dsName=csi-azurefile2-node-win --set node.livenessProbe.healthPort=39613
```

### search for all available chart versions
```console
helm search repo -l azurefile-csi-driver
```

## uninstall CSI driver
```console
helm uninstall azurefile-csi-driver -n kube-system
```

## latest chart configuration

The following table lists the configurable parameters of the latest Azure File CSI Driver chart and default values.

| Parameter                                         | Description                                                | Default                                                           |
|---------------------------------------------------|------------------------------------------------------------|-------------------------------------------------------------------|
| `driver.name`                                     | alternative driver name                                    | `file.csi.azure.com` |
| `driver.customUserAgent`                          | custom userAgent               | `` |
| `driver.userAgentSuffix`                          | userAgent suffix               | `OSS-helm` |
| `feature.enableFSGroupPolicy`                     | enable `fsGroupPolicy` on a k8s 1.20+ cluster(only applied for NFS protocol)              | `false`                      |
| `feature.enableGetVolumeStats`                    | allow GET_VOLUME_STATS on agent node                  | `false`                      |
| `image.baseRepo`                                  | base repository of driver images                           | `mcr.microsoft.com`                      |
| `image.azurefile.repository`                      | azurefile-csi-driver docker image                          | `/k8s/csi/azurefile-csi`                            |
| `image.azurefile.tag`                             | azurefile-csi-driver docker image tag                      | `latest`                                                            |
| `image.azurefile.pullPolicy`                      | azurefile-csi-driver image pull policy                     | `IfNotPresent`                                                      |
| `image.csiProvisioner.repository`                 | csi-provisioner docker image                               | `/oss/kubernetes-csi/csi-provisioner`              |
| `image.csiProvisioner.tag`                        | csi-provisioner docker image tag                           | `v2.2.2`                                                            |
| `image.csiProvisioner.pullPolicy`                 | csi-provisioner image pull policy                          | `IfNotPresent`                                                      |
| `image.csiAttacher.repository`                    | csi-attacher docker image                                  | `/oss/kubernetes-csi/csi-attacher`                 |
| `image.csiAttacher.tag`                           | csi-attacher docker image tag                              | `v3.3.0`                                                            |
| `image.csiAttacher.pullPolicy`                    | csi-attacher image pull policy                             | `IfNotPresent`                                                      |
| `image.csiResizer.repository`                     | csi-resizer docker image                                   | `/oss/kubernetes-csi/csi-resizer`                  |
| `image.csiResizer.tag`                            | csi-resizer docker image tag                               | `v1.3.0`                                                            |
| `image.csiResizer.pullPolicy`                     | csi-resizer image pull policy                              | `IfNotPresent`                                                      |
| `image.livenessProbe.repository`                  | liveness-probe docker image                                | `/oss/kubernetes-csi/livenessprobe`                |
| `image.livenessProbe.tag`                         | liveness-probe docker image tag                            | `v2.5.0`                                                            |
| `image.livenessProbe.pullPolicy`                  | liveness-probe image pull policy                           | `IfNotPresent`                                                      |
| `image.nodeDriverRegistrar.repository`            | csi-node-driver-registrar docker image                     | `/oss/kubernetes-csi/csi-node-driver-registrar`    |
| `image.nodeDriverRegistrar.tag`                   | csi-node-driver-registrar docker image tag                 | `v2.4.0`                                                            |
| `image.nodeDriverRegistrar.pullPolicy`            | csi-node-driver-registrar image pull policy                | `IfNotPresent`                                                      |
| `imagePullSecrets`                                | Specify docker-registry secret names as an array           | [] (does not add image pull secrets to deployed pods)             |
| `customLabels`                                    | Custom labels to add into metadata                         | `{}`                                                                |
| `serviceAccount.create`                           | whether create service account of csi-azurefile-controller, csi-azurefile-node, and snapshot-controller| `true`                                                    |
| `serviceAccount.controller`                       | name of service account for csi-azurefile-controller       | `csi-azurefile-controller-sa`                                  |
| `serviceAccount.node`                             | name of service account for csi-azurefile-node             | `csi-azurefile-node-sa`                                        |
| `serviceAccount.snapshotController`               | name of service account for csi-snapshot-controller        | `csi-snapshot-controller-sa`                                   |
| `rbac.create`                                     | whether create rbac for this driver     | `true`                                                              |
| `rbac.name`                                       | driver name in rbac role                | `azurefile`                                                         |
| `controller.name`                                 | name of driver deployment                  | `csi-azurefile-controller`
| `controller.cloudConfigSecretName`                | cloud config secret name of controller driver               | `azure-cloud-provider`
| `controller.cloudConfigSecretNamespace`           | cloud config secret namespace of controller driver          | `kube-system`
| `controller.allowEmptyCloudConfig`                | Whether allow running controller driver without cloud config          | `true`
| `controller.replicas`                             | replicas of csi-azurefile-controller                    | `2`                                                                 |
| `controller.hostNetwork`                          | `hostNetwork` setting on controller driver(could be disabled if controller does not depend on MSI setting)                            | `true`                                                            | `true`, `false`
| `controller.metricsPort`                          | metrics port of csi-azurefile-controller                   |`29614`                                                        |
| `controller.livenessProbe.healthPort `            | health check port for liveness probe                   | `29612` |
| `controller.runOnMaster`                          | run controller on master node                                                          |`false`                                                           |
| `controller.attachRequired`                       | enable attach/detach (only valid for vhd disk feature)                                            |`false`                                                           |
| `controller.logLevel`                             | controller driver log level                                                          |`5`                                                           |
| `controller.resources.csiProvisioner.limits.cpu`      | csi-provisioner cpu limits                            | 1                                                           |
| `controller.resources.csiProvisioner.limits.memory`   | csi-provisioner memory limits                         | 500Mi                                                          |
| `controller.resources.csiProvisioner.requests.cpu`    | csi-provisioner cpu requests limits                   | 10m                                                            |
| `controller.resources.csiProvisioner.requests.memory` | csi-provisioner memory requests limits                | 20Mi                                                           |
| `controller.resources.csiAttacher.limits.cpu`         | csi-attacher cpu limits                            | 1                                                           |
| `controller.resources.csiAttacher.limits.memory`      | csi-attacher memory limits                         | 500Mi                                                          |
| `controller.resources.csiAttacher.requests.cpu`       | csi-attacher cpu requests limits                   | 10m                                                            |
| `controller.resources.csiAttacher.requests.memory`    | csi-attacher memory requests limits                | 20Mi                                                           |
| `controller.resources.csiResizer.limits.cpu`          | csi-resizer cpu limits                            | 1                                                           |
| `controller.resources.csiResizer.limits.memory`       | csi-resizer memory limits                         | 500Mi                                                          |
| `controller.resources.csiResizer.requests.cpu`        | csi-resizer cpu requests limits                   | 10m                                                            |
| `controller.resources.csiResizer.requests.memory`     | csi-resizer memory requests limits                | 20Mi                                                           |
| `controller.resources.csiSnapshotter.limits.cpu`      | csi-snapshotter cpu limits                            | 1                                                           |
| `controller.resources.csiSnapshotter.limits.memory`   | csi-snapshotter memory limits                         | 500Mi                                                          |
| `controller.resources.csiSnapshotter.requests.cpu`    | csi-snapshotter cpu requests limits                   | 10m                                                            |
| `controller.resources.csiSnapshotter.requests.memory` | csi-snapshotter memory requests limits                | 20Mi                                                           |
| `controller.resources.livenessProbe.limits.cpu`       | liveness-probe cpu limits                             | 1                                                           |
| `controller.resources.livenessProbe.limits.memory`    | liveness-probe memory limits                          | 100Mi                                                          |
| `controller.resources.livenessProbe.requests.cpu`     | liveness-probe cpu requests limits                    | 10m                                                            |
| `controller.resources.livenessProbe.requests.memory`  | liveness-probe memory requests limits                 | 20Mi                                                           |
| `controller.resources.azurefile.limits.cpu`           | azurefile cpu limits                            | 1                                                           |
| `controller.resources.azurefile.limits.memory`        | azurefile memory limits                         | 200Mi                                                          |
| `controller.resources.azurefile.requests.cpu`         | azurefile cpu requests limits                   | 10m                                                            |
| `controller.resources.azurefile.requests.memory`      | azurefile memory requests limits                | 20Mi                                                           |
| `controller.kubeconfig`                           | configure kubeconfig path on controller node                | '' (empty, use InClusterConfig by default)
| `controller.tolerations`                          | controller pod tolerations                            |                                                              |
| `node.cloudConfigSecretName`                      | cloud config secret name of node driver               | `azure-cloud-provider`
| `node.cloudConfigSecretNamespace`                 | cloud config secret namespace of node driver          | `kube-system`
| `node.allowEmptyCloudConfig`                      | Whether allow running node driver without cloud config          | `true`
| `node.maxUnavailable`                             | `maxUnavailable` value of driver node daemonset                            | `1`
| `node.metricsPort`                                | metrics port of csi-azurefile-node                         |`29615`                                                       |
| `node.livenessProbe.healthPort `                  | health check port for liveness probe                   | `29613` |
| `node.logLevel`                                   | node driver log level                                                          |`5`                                                           |
| `snapshot.enabled`                                | whether enable snapshot feature                            | `false`                                                        |
| `snapshot.image.csiSnapshotter.repository`        | csi-snapshotter docker image                               | `/oss/kubernetes-csi/csi-snapshotter`         |
| `snapshot.image.csiSnapshotter.tag`               | csi-snapshotter docker image tag                           | `v3.0.3`                                                       |
| `snapshot.image.csiSnapshotter.pullPolicy`        | csi-snapshotter image pull policy                          | `IfNotPresent`                                                 |
| `snapshot.image.csiSnapshotController.repository` | snapshot-controller docker image                           | `/oss/kubernetes-csi/snapshot-controller`     |
| `snapshot.image.csiSnapshotController.tag`        | snapshot-controller docker image tag                       | `v3.0.3`                                                       |
| `snapshot.image.csiSnapshotController.pullPolicy` | snapshot-controller image pull policy                      | `IfNotPresent`                                                 |
| `snapshot.snapshotController.name`                | snapshot controller name                                   | `csi-snapshot-controller`                                                           |
| `snapshot.snapshotController.replicas`            | the replicas of snapshot-controller                        | `2`                                                          |
| `snapshot.snapshotController.resources.limits.cpu`             | csi-snapshot-controller cpu limits                             | 1                                                           |
| `snapshot.snapshotController.resources.limits.memory`          | csi-snapshot-controller memory limits                          | 100Mi                                                          |
| `snapshot.snapshotController.resources.requests.cpu`           | csi-snapshot-controller cpu requests limits                    | 10m                                                            |
| `snapshot.snapshotController.resources.requests.memory`        | csi-snapshot-controller memory requests limits                 | 20Mi                                                           |
| `linux.enabled`                                   | whether enable linux feature                               | `true`                                                              |
| `linux.dsName`                                    | name of driver daemonset on linux                             |`csi-azurefile-node`                                                         |
| `linux.dnsPolicy`                                 | dnsPolicy setting of driver daemonset on linux                             | `Default` (available values: `Default`, `ClusterFirst`, `ClusterFirstWithHostNet`, `None`)
| `linux.kubelet`                                   | configure kubelet directory path on Linux agent node node                  | `/var/lib/kubelet`                                                |
| `linux.kubeconfig`                                | configure kubeconfig path on Linux agent node                | '' (empty, use InClusterConfig by default)                                            |
| `linux.distro`                                    | configure ssl certificates for different Linux distribution(available values: `debian`, `fedora`)                  |
| `linux.tolerations`                               | linux node driver tolerations                            |
| `linux.resources.livenessProbe.limits.cpu`             | liveness-probe cpu limits                             | 1                                                           |
| `linux.resources.livenessProbe.limits.memory`          | liveness-probe memory limits                          | 100Mi                                                          |
| `linux.resources.livenessProbe.requests.cpu`           | liveness-probe cpu requests limits                    | 10m                                                            |
| `linux.resources.livenessProbe.requests.memory`        | liveness-probe memory requests limits                 | 20Mi                                                           |
| `linux.resources.nodeDriverRegistrar.limits.cpu`       | csi-node-driver-registrar cpu limits                  | 200m                                                           |
| `linux.resources.nodeDriverRegistrar.limits.memory`    | csi-node-driver-registrar memory limits               | 100Mi                                                          |
| `linux.resources.nodeDriverRegistrar.requests.cpu`     | csi-node-driver-registrar cpu requests limits         | 10m                                                            |
| `linux.resources.nodeDriverRegistrar.requests.memory`  | csi-node-driver-registrar memory requests limits      | 20Mi                                                           |
| `linux.resources.azurefile.limits.cpu`                 | azurefile cpu limits                            | 1                                                            |
| `linux.resources.azurefile.limits.memory`              | azurefile memory limits                         | 200Mi                                                         |
| `linux.resources.azurefile.requests.cpu`               | azurefile cpu requests limits                   | 10m                                                            |
| `linux.resources.azurefile.requests.memory`            | azurefile memory requests limits                | 20Mi                                                           |
| `windows.enabled`                                 | whether enable windows feature                             | `true`                                                             |
| `windows.dsName`                                  | name of driver daemonset on windows                             |`csi-azurefile-node-win`                                                         |
| `windows.kubelet`                                 | configure kubelet directory path on Windows agent node                | `'C:\var\lib\kubelet'`                                            |
| `windows.kubeconfig`                              | configure kubeconfig path on Windows agent node                | `'C:\k\config'`                                            |
| `windows.tolerations`                             | windows node driver tolerations                            |                                                              |
| `windows.resources.livenessProbe.limits.cpu`             | liveness-probe cpu limits                             | 1                                                           |
| `windows.resources.livenessProbe.limits.memory`          | liveness-probe memory limits                          | 200Mi                                                          |
| `windows.resources.livenessProbe.requests.cpu`           | liveness-probe cpu requests limits                    | 10m                                                            |
| `windows.resources.livenessProbe.requests.memory`        | liveness-probe memory requests limits                 | 20Mi                                                           |
| `windows.resources.nodeDriverRegistrar.limits.cpu`       | csi-node-driver-registrar cpu limits                  | 200m                                                           |
| `windows.resources.nodeDriverRegistrar.limits.memory`    | csi-node-driver-registrar memory limits               | 200Mi                                                          |
| `windows.resources.nodeDriverRegistrar.requests.cpu`     | csi-node-driver-registrar cpu requests limits         | 10m                                                            |
| `windows.resources.nodeDriverRegistrar.requests.memory`  | csi-node-driver-registrar memory requests limits      | 20Mi                                                           |
| `windows.resources.azurefile.limits.cpu`                 | azurefile cpu limits                            | 1                                                            |
| `windows.resources.azurefile.limits.memory`              | azurefile memory limits                         | 400Mi                                                         |
| `windows.resources.azurefile.requests.cpu`               | azurefile cpu requests limits                   | 10m                                                            |
| `windows.resources.azurefile.requests.memory`            | azurefile memory requests limits                | 20Mi                                                           |

## troubleshooting
 - Add `--wait -v=5 --debug` in `helm install` command to get detailed error
 - Use `kubectl describe` to acquire more info
