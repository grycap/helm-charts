# OSCAR - Open Source Serverless Computing for Data-Processing Applications

OSCAR is an open-source platform to support Serverless computing for file-processing applications. It can be automatically deployed on multi-Clouds in order to create highly-parallel event-driven file-processing serverless applications that execute on customized runtime environments provided by Docker containers than run on an elastic Kubernetes cluster.

## MinIO installation

OSCAR depends on MinIO for accessing the files to be processed and to store the results after services executions. The installation of MinIO has not been included as a dependency of this chart because there are multiple ways to install and configure it. Please follow the [official documentation](https://docs.min.io/docs/deploy-minio-on-kubernetes.html) to deploy it.

It is important to have MinIO deployed on the same Kubernetes cluster as OSCAR in order to create the input/output bucket and folders, as well as correctly configure event notifications. Granting access to MinIO in an OSCAR deployment must be done through the chart [configuration values](#configuration).

## Helm Chart

To install the OSCAR chart first add the GRyCAP repo:

```sh
helm repo add grycap https://grycap.github.io/helm-charts/
```

Create the `oscar` and `oscar-svc` namespaces if they don't exist:

```sh
kubectl apply -f https://raw.githubusercontent.com/grycap/oscar/master/deploy/yaml/oscar-namespaces.yaml
```

Then install the OSCAR chart (with Helm v2):

```sh
helm install --namespace=oscar --name=oscar grycap/oscar
```

Then install the IM chart (with Helm v3):

```sh
helm install --namespace=oscar oscar grycap/oscar
```

*OSCAR can be exposed by a Kubernetes Ingress, so it expects an ingress controller installed and configured in the cluster.*

### Configuration

The following table lists the configurable parameters of the OSCAR chart and their default values.

| Parameter                               | Description                                                                                                                                                                                           | Default                              |
| --------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| `servicesNamespace`                     | Namespace to deploy OSCAR services                                                                                                                                                                    | `oscar-svc`                          |
| `authUser`                              | Username for basic-auth                                                                                                                                                                               | `oscar`                              |
| `authPass`                              | Password for basic-auth                                                                                                                                                                               | `oscar123`                           |
| `serverlessBackend`                     | Enable a Serverless backend for sync invocations (disabled if not set) (`openfaas`\|`knative`\|`""`)                                                                                                  | `""`                                 |
| `openfaas.namespace`                    | Namespace where the OpenFaaS gateway is deployed                                                                                                                                                      | `openfaas`                           |
| `openfaas.port`                         | Service port where the OpenFaaS gateway is exposed (ClusterIP)                                                                                                                                        | `8080`                               |
| `openfaas.basicAuthSecret`              | Name of the secret where the OpenFaaS basic auth credentials are stored                                                                                                                               | `basic-auth`                         |
| `openfaas.prometheusPort`               | Service port where the OpenFaaS prometheus server is exposed (ClusterIP)                                                                                                                              | `9090`                               |
| `openfaas.scaler.enable`                | Option to enable the bundled OpenFaaS scaler                                                                                                                                                          | `false`                              |
| `openfaas.scaler.interval`              | Time interval to check if there are idle OpenFaaS functions                                                                                                                                           | `2m`                                 |
| `openfaas.scaler.inactivityDuration`    | Duration with no requests to consider that an OpenFaaS function is idle                                                                                                                               | `10m`                                |
| `openfaas.watchdog.maxInflight`         | Limit the maximum number of requests in flight for the OpenFaaS watchdog                                                                                                                              | `1`                                  |
| `openfaas.watchdog.writeDebug`          | Write all output, error messages, and additional information to the logs for the OpenFaaS watchdog                                                                                                    | `true`                               |
| `openfaas.watchdog.execTimeout`         | Exec timeout for process exec'd for each incoming request (in seconds). Disabled if set to 0                                                                                                          | `0`                                  |
| `openfaas.watchdog.readTimeout`         | HTTP timeout for reading the payload from the client caller (in seconds)                                                                                                                              | `300`                                |
| `openfaas.watchdog.writeTimeout`        | HTTP timeout for writing a response body from your function (in seconds)                                                                                                                              | `300`                                |
| `openfaas.watchdog.healthCheckInterval` | Time interval to check if the watchdog is not processing any request in order to exit earlier during graceful shutdown (in seconds)                                                                   | `5`                                  |
| `yuniKorn.enable`                       | Option to enable the integration with [Apache YuniKorn](https://yunikorn.apache.org/)                                                                                                                 | `false`                              |
| `yuniKorn.namespace`                    | Namespace where the Apache YuniKorn's scheduler is deployed                                                                                                                                           | `yunikorn`                           |
| `yuniKorn.configMap`                    | Name of the Apache YuniKorn's configMap                                                                                                                                                               | `yunikorn-configs`                   |
| `yuniKorn.configFileName`               | Name of the Apache YuniKorn's filename inside the configMap                                                                                                                                           | `queues.yaml`                        |
| `resourceManager.enable`                | Option to enable the Resource Manager to delegate jobs when there are no available resources in the cluster (if the service has replicas)                                                             | `false`                              |
| `resourceManager.interval`              | Time interval (in seconds) to update the available resources in the cluster                                                                                                                           | `15`                                 |
| `reScheduler.enable`                    | Option to enable the ReScheduler to delegate jobs to a replica                                                                                                                                        | `false`                              |
| `reScheduler.interval`                  | Time interval (in seconds) to check if pending jobs                                                                                                                                                   | `15`                                 |
| `reScheduler.threshold`                 | Time (in seconds) that a job (with replicas) can be queued before delegating it                                                                                                                       | `30`                                 |
| `oidc.enable`                           | Parameter to enable OpenID Connect support                                                                                                                                                            | `false`                              |
| `oidc.issuer`                           | OpenID Connect issuer as returned in the "iss" field of the JWT payload                                                                                                                               | `https://aai.egi.eu/auth/realms/egi` |
| `oidc.subject`                          | OpenID Connect Subject (user identifier)                                                                                                                                                              | `""`                                 |
| `oidc.groups`                           | OpenID comma-separated group list to grant access in the cluster. Groups defined in the "eduperson_entitlement" OIDC scope, as described [here](https://docs.egi.eu/providers/check-in/sp/#10-groups) | `""`                                 |
| `service.type`                          | Type of service to expose OSCAR                                                                                                                                                                       | `NodePort`                           |
| `service.port`                          | ClusterIP port for OSCAR                                                                                                                                                                              | `8080`                               |
| `service.nodePort`                      | NodePort to expose OSCAR (if `service.type` is `NodePort`)                                                                                                                                            | `30301`                              |
| `replicas`                              | Number of replicas to deploy                                                                                                                                                                          | `1`                                  |
| `ingress.create`                        | Create a Kubernetes ingress to expose OSCAR                                                                                                                                                           | `false`                              |
| `ingress.host`                          | DNS Host for the ingress                                                                                                                                                                              | `""`                                 |
| `ingress.certManagerIssuer`             | Cert-manager issuer to automatically assign TLS certificates                                                                                                                                          | `""`                                 |
| `image.repository`                      | Docker Hub image to deploy OSCAR                                                                                                                                                                      | `ghcr.io/grycap/oscar`               |
| `image.tag`                             | Release tag to install                                                                                                                                                                                | `2.6.0`                              |
| `image.pullPolicy`                      | Image [pullPolicy](https://kubernetes.io/docs/concepts/containers/images/#updating-images)                                                                                                            | `Always`                             |
| `readTimeout`                           | Read timeout for OSCAR's HTTP server (in seconds)                                                                                                                                                     | `300`                                |
| `writeTimeout`                          | Write timeout for OSCAR's HTTP server (in seconds)                                                                                                                                                    | `300`                                |
| `minIO.endpoint`                        | MinIO endpoint                                                                                                                                                                                        | `https://minio-service.minio:9000`   |
| `minIO.TLSVerify`                       | Verify MinIO's TLS certificates for HTTPS connections                                                                                                                                                 | `true`                               |
| `minIO.region`                          | Region of the MinIO server                                                                                                                                                                            | `us-east-1`                          |
| `minIO.accessKey`                       | Access key of the MinIO server                                                                                                                                                                        | `minio`                              |
| `minIO.secretKey`                       | Secret key of the MinIO server                                                                                                                                                                        | `minio123`                           |
| `volume.supervisorVersion`              | [FaaS Supervisor](https://github.com/grycap/faas-supervisor) version to use if ServerlessBackend is enabled                                                                                           | `1.5.7`                              |
| `volume.watchdogVersion`                | [OpenFaaS Watchdog](https://github.com/openfaas/classic-watchdog) version to use in the OSCAR's services                                                                                              | `0.2.1`                              |
| `volume.storageClassName`               | Storage Class used by the PVC                                                                                                                                                                         | `""`                                 |
| `resources.requests.memory`             | Memory resource requests                                                                                                                                                                              | `512Mi`                              |
| `resources.requests.cpu`                | CPU resource requests                                                                                                                                                                                 | `500m`                               |
| `additionalConfig.allowedImagePrefix` | List of repository prefixes to restrict Docker images | `""` |

