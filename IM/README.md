# IM - Infrastructure Manager

IM is a tool that deploys complex and customized virtual infrastructures on IaaS
Cloud deployments (such as AWS, OpenStack, etc.). It eases the access and the
usability of IaaS clouds by automating the VMI (Virtual Machine Image)
selection, deployment, configuration, software installation, monitoring and
update of the virtual infrastructure. It supports APIs from a large number of virtual
platforms, making user applications cloud-agnostic. In addition it integrates a
contextualization system to enable the installation and configuration of all the
user required applications providing the user with a fully functional
infrastructure.

Read the documentation and more at http://www.grycap.upv.es/im.

There is also an Infrastructure Manager YouTube reproduction list with a set of videos with demos
of the functionality of the platform: https://www.youtube.com/playlist?list=PLgPH186Qwh_37AMhEruhVKZSfoYpHkrUp.

Please acknowledge the use of this software in your scientific publications by including the following reference:

Miguel Caballer, Ignacio Blanquer, German Molto, and Carlos de Alfonso. "[Dynamic management of virtual infrastructures](https://link.springer.com/article/10.1007/s10723-014-9296-5)". Journal of Grid Computing, Volume 13, Issue 1, Pages 53-70, 2015, ISSN 1570-7873, DOI: 10.1007/s10723-014-9296-5.

## 1 Helm Chart

How to install the IM chart.

First add the GRyCAP repo:

```sh
helm repo add grycap https://grycap.github.io/helm-charts/
```

Then install the IM chart (with Helm v2):

```sh
helm install --namespace=im --name=im  grycap/IM
```

Then install the IM chart (with Helm v3):

```sh
helm install --namespace=im --create-namespace im  grycap/IM
```

The IM chat uses ingress rules to publish the external services (IM service and IM-web portal). So it expects an ingress
controller installed and configured in the Kubernetes cluster.

## Configuration

The following table lists the configurable parameters of the IM chart and their default values.

| Parameter                         | Description                                                 | Default          |
| --------------------------------- | ----------------------------------------------------------- | ---------------- |
| `mysql.mysqlUser`                 | Username of IM `MySQL` user to create.                      | `imuser`         |
| `mysql.mysqlPassword`             | Password for the new `MySQL` IM user.                       | `impass`         |
| `mysql.persistence.enabled`       | Create a volume to store `MySQL` data.                      | `false`          |
| `mysql.persistence.storageClass`  | Type of PVC for `MySQL`.                                    | `imdb`           |
| `mysql.persistence.accessMode`    | Access mode of the `MySQL` PVC.                             | `ReadWriteOnce`  |
| `mysql.persistence.size`          | Size of PVC for `MySQL`.                                    | `8Gi`            |
| `im.version`                      | `im` docker image version                                   | `latest`         |
| `im.resources.requests.memory`    | `im` Memory resource requests/limits                        | `256Mi`          |
| `im.resources.requests.cpu`       | `im` CPU resource requests/limits                           | `250m`           |
| `im.replicas`                     | Number of IM Pods to run ([see IM HA mode](#IM-HA-mode)).   | `1`              |
| `im.log.level`                    | IM Log Level                                                | `DEBUG`          |
| `im.log.persistence.enabled`      | Create a volume to store `log` data.                        | `false`          |
| `im.log.persistence.storageClass` | Type of PVC for `log`.                                      | `imlog`          |
| `im.log.persistence.accessMode`   | Access mode of the `log` PVC.                               | `ReadWriteOnce`  |
| `im.log.persistence.size`         | Size of PVC for `log`.                                      | `8Gi`            |
| `im.config`                       | List of IM configuration values. (See [IM docs](https://imdocs.readthedocs.io/en/latest/manual.html#configuration)).             | `[]`             |
| `imweb.version`                   | `im-web` docker image version                               | `latest`         |
| `imweb.resources.requests.memory` | `im-web` Memory resource requests/limits                    | `256Mi`          |
| `imweb.resources.requests.cpu`    | `im-web` CPU resource requests/limits                       | `250m`           |
| `imdashboard.deploy`              | Deploy `im-dashboard` web interface.                        | `false`          |
| `imdashboard.version`             | `im-dashboard` docker image version.                        | `latest`         |
| `imdashboard.resources.requests.memory` | `im-dashboard` Memory resource requests/limits                        | `256Mi`          |
| `imdashboard.resources.requests.cpu`    | `im-dashboard` CPU resource requests/limits                           | `250m`           |
| `imdashboard.oidc.name`           | `im-dashboard` OIDC provider name.                          | `name`           |
| `imdashboard.oidc.base_url`       | `im-dashboard` OIDC provider base URL.                      | `url`            |
| `imdashboard.oidc.client_id`      | `im-dashboard` OIDC provider Client ID.                     | `id`             |
| `imdashboard.oidc.client_secret`  | `im-dashboard` OIDC provider Client Secret.                 | `sec`            |
| `imdashboard.oidc.client_secret`  | `im-dashboard` OIDC provider Client Secret.                 | `sec`            |
| `imdashboard.oidc.group_membership` | `im-dashboard` OIDC list of groups needed to access the dashboard.           | `[]`                |
| `imdashboard.oidc.scopes`  | `im-dashboard` OIDC list of scopes.                 | `openid email profile offline_access eduperson_entitlement'`            |
| `imdashboard.support_email`       | `im-dashboard` support email.                               | `root@server.com`          |
| `imdashboard.analytics_tag`       | `im-dashboard` Google Anaytics tag.                         | `""`             |
| `imdashboard.loglevel`            | `im-dashboard` Log Level.                                   | `info`           |
| `imdashboard.static_sites`        | `im-dashboard` List of static sites.                        | `[]`             |
| `imdashboard.credentials_key`     | `im-dashboard` Credentials encryption key (read [this](https://github.com/grycap/im-dashboard#enabling-credentials-encryption)).                  | `PXZ66574VjKIMSRXPWquRbcH8HaxH2yPRYsgZljlclA=`             |
| `imdashboard.external_links`      | `im-dashboard` List of external links.                        | `[ { "url": "https://imdocs.readthedocs.io/", "menu_item_name": "IM Docs" }, { "url": "https://youtu.be/vmtzGOZxiUg", "menu_item_name": "IM Video Demo" }]'`             |


## IM HA mode

In case of setting more than 1 in the number of IM Pods to run (`im.replicas`) the IM service will be launched in
HA mode. It requires an [HAProxy](http://www.haproxy.org/) load balanced on top of them. Furtemore a [rsyslog](https://www.rsyslog.com/)
service is also required to manage the log information of all the IM pods.