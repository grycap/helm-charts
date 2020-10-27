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

| Parameter                   | Description                                                                                         | Default                            |
| --------------------------- | --------------------------------------------------------------------------------------------------- | ---------------------------------- |
| `servicesNamespace`         | Namespace to deploy OSCAR services                                                                  | `oscar-svc`                        |
| `authUser`                  | Username for basic-auth                                                                             | `oscar`                            |
| `authPass`                  | Password for basic-auth                                                                             | `oscar123`                         |
| `serverlessBackend`         | Enable a Serverless backend for sync invocations (disabled if not set) (`openfaas`\|`""`)            | `""`                               |
| `openfaas.namespace`         | Namespace where the OpenFaaS gateway is deployed                                                    | `openfaas`                         |
| `openfaas.port`              | Service port where the OpenFaaS gateway is exposed (ClusterIP)                                      | `8080`                             |
| `service.type`              | Type of service to expose OSCAR                                                                     | `NodePort`                         |
| `service.port`              | ClusterIP port for OSCAR                                                                            | `8080`                             |
| `service.nodePort`          | NodePort to expose OSCAR (if `service.type` is `NodePort`)                                          | `30301`                            |
| `replicas`                  | Number of replicas to deploy                                                                        | `1`                                |
| `createIngress`             | Create a Kubernetes ingress to expose OSCAR                                                         | `false`                            |
| `image.repository`          | Docker Hub image to deploy OSCAR                                                                    | `grycap/oscar`                     |
| `image.tag`                 | Release tag to install                                                                              | `2.0.0-beta3`                      |
| `image.pullPolicy`          | Image [pullPolicy](https://kubernetes.io/docs/concepts/containers/images/#updating-images)          | `Always`                           |
| `readTimeout`               | Read timeout for OSCAR's HTTP server (in seconds)                                                   | `300`                              |
| `writeTimeout`              | Write timeout for OSCAR's HTTP server (in seconds)                                                  | `300`                              |
| `minIO.endpoint`            | MinIO endpoint                                                                                      | `https://minio-service.minio:9000` |
| `minIO.TLSVerify`           | Verify MinIO's TLS certificates for HTTPS connections                                               | `true`                             |
| `minIO.region`              | Region of the MinIO server                                                                          | `us-east-1`                        |
| `minIO.accessKey`           | Access key of the MinIO server                                                                      | `minio`                            |
| `minIO.secretKey`           | Secret key of the MinIO server                                                                      | `minio123`                         |
| `volume.supervisorVersion`  | [FaaS Supervisor](https://github.com/grycap/faas-supervisor) version to use in the OSCAR's services | `1.2.4-beta3`                      |
| `resources.requests.memory` | Memory resource requests                                                                            | `256Mi`                            |
| `resources.requests.cpu`    | CPU resource requests                                                                               | `250m`                             |

