# Gatus Helm Chart

[![CI Status](https://img.shields.io/github/workflow/status/avakarev/gatus-chart/Test%20Workflow/main?longCache=tru&label=CI%20Status&logo=github%20actions&logoColor=fff)](https://github.com/avakarev/gatus-chart/actions?query=branch%3Amain+workflow%3A%22Test+Workflow%22)
[![Artifact HUB](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/gatus)](https://artifacthub.io/packages/helm/gatus/gatus)
[![License: MIT](https://img.shields.io/github/license/avakarev/gatus-chart)](https://github.com/avakarev/gatus-chart/blob/main/LICENSE)

> Installs the automated service health dashboard [Gatus](https://github.com/TwiN/gatus)

## Get Repo Info

```console
helm repo add gatus https://avakarev.github.io/gatus-chart
helm repo update
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Install Chart

```console
helm install --name [RELEASE_NAME] gatus/gatus
```

_See [configuration](#configuration) below._
_See [helm install](https://helm.sh/docs/helm/helm_install/) for command documentation._

## Uninstall Chart

```console
helm delete --purge [RELEASE_NAME]
```

This removes all the Kubernetes components associated with the chart and deletes the release.
_See [helm uninstall](https://helm.sh/docs/helm/helm_uninstall/) for command documentation._

## Upgrading an existing Release to a new major version

### To 2.0.0

This version requires Helm >= 3
Gatus version is upgraded from 2 to 3. Gatus 3 deprecates `memory` type of storage, supports `sqlite` and `postgres`.
`storage` is not part of `persistence` anymore and is part of `config` now. Check the example below.

## Configuration

| Parameter                                 | Description                                     | Default                              |
|-------------------------------------------|-------------------------------------------------|--------------------------------------|
| `deploymentStrategy`                      | Deployment strategy                             | `RollingUpdate`                      |
| `readinessProbe.enabled`                  | Enable readiness probe                          | `true`                               |
| `livenessProbe.enabled`                   | Enable liveness probe                           | `true`                               |
| `image.repository`                        | Image repository                                | `twinproduction/gatus`               |
| `image.tag`                               | Image tag                                       | `v3.7.0`                             |
| `image.pullPolicy`                        | Image pull policy                               | `IfNotPresent`                       |
| `image.pullSecrets`                       | Image pull secrets                              | `{}`                                 |
| `hostNetwork.enabled`                     | Enable host network mode                        | `false`                              |
| `annotations`                             | Deployment annotations                          | `{}`                                 |
| `labels`                                  | Deployment labels                               | `{}`                                 |
| `podAnnotations`                          | Pod annotations                                 | `{}`                                 |
| `podLabels`                               | Pod labels                                      | `{}`                                 |
| `serviceAccount.create`                   | Create service account                          | `false`                              |
| `serviceAccount.name`                     | Service account name to use                     | ``                                   |
| `serviceAccount.annotations`              | ServiceAccount annotations                      | `{}`                                 |
| `serviceAccount.autoMount`                | Automount the service account token in the pod  | `false`                              |
| `podSecurityContext.fsGroup`              | Pod volume's ownership GID                      | `65534`                              |
| `securityContext.runAsNonRoot`            | Container runs as a non-root user               | `true`                               |
| `securityContext.runAsUser`               | Container processes' UID to run the entrypoint  | `65534`                              |
| `securityContext.runAsGroup`              | Container processes' GID to run the entrypoint  | `65534`                              |
| `securityContext.readOnlyRootFilesystem`  | Container's root filesystem is read-only        | `true`                               |
| `service.type`                            | Type of service                                 | `ClusterIP`                          |
| `service.port`                            | Port for kubernetes service                     | `80`                                 |
| `service.annotations`                     | Service annotations                             | `{}`                                 |
| `service.labels`                          | Custom labels                                   | `{}`                                 |
| `ingress.enabled`                         | Enables Ingress                                 | `false`                              |
| `ingress.annotations`                     | Ingress annotations (values are templated)      | `{}`                                 |
| `ingress.labels`                          | Custom labels                                   | `{}`                                 |
| `ingress.path`                            | Ingress accepted path                           | `/`                                  |
| `ingress.pathType`                        | Ingress type of path                            | `Prefix`                             |
| `ingress.extraPaths`                      | Ingress extra paths to prepend to every host    | `[]`                                 |
| `ingress.hosts`                           | Ingress accepted hostnames                      | `["chart-example.local"]`            |
| `ingress.tls`                             | Ingress TLS configuration                       | `[]`                                 |
| `env`                                     | Extra environment variables passed to pods      | `{}`                                 |
| `extraVolumeMounts`                       | Extra volume mounts                             | `[]`                                 |
| `secrets`                                 | Include secret's content in pod environment     | `false`                              |
| `resources`                               | CPU/Memory resource requests/limits             | `{}`                                 |
| `nodeSelector`                            | Node labels for pod assignment                  | `{}`                                 |
| `persistence.enabled`                     | Use persistent volume to store data             | `false`                              |
| `persistence.size`                        | Size of persistent volume claim                 | `200Mi`                              |
| `persistence.mounthPath`                  | Persistent data volume's mount path             | `/data`                              |
| `persistence.subPath`                     | Mount a sub dir of the persistent volume        | `nil`                                |
| `persistence.accessModes`                 | Persistence access modes                        | `[ReadWriteOnce]`                    |
| `persistence.finalizers`                  | PersistentVolumeClaim finalizers                | `["kubernetes.io/pvc-protection"]`   |
| `persistence.annotations`                 | PersistentVolumeClaim annotations               | `{}`                                 |
| `persistence.selectorLabels`              | PersistentVolumeClaim selector labels           | `{}`                                 |
| `persistence.existingClaim`               | Use an existing PVC to persist data             | `nil`                                |
| `persistence.storageClassName`            | Type of persistent volume claim                 | `nil`                                |
| `config`                                  | [Gatus configuration][gatus-config]             | `{}`                                 |

_See [Customizing the Chart Before Installing](https://helm.sh/docs/intro/using_helm/#customizing-the-chart-before-installing)._

To see all configurable options with detailed comments, visit the chart's [values.yaml](./gatus/values.yaml), or run

```console
helm inspect values gatus/gatus
```

### `helmfile.yaml` example

```yaml
---
repositories:
  - name: gatus
    url: https://avakarev.github.io/gatus-chart

releases:
  - name: gatus
    namespace: gatus
    chart: gatus/gatus
    version: 2.2.1
    values:
      - persistence:
          enabled: true
      - config:
          storage:
            type: sqlite
            file: /data/data.db
          endpoints:
            - name: Example
              url: https://example.com
              conditions:
                - '[STATUS] == 200'
```

## License

`gatus-chart` is licensed under MIT license. (see [LICENSE](./LICENSE))


[gatus-config]: https://github.com/TwiN/gatus#configuration
