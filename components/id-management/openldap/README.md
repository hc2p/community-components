# OpenLDAP Helm Chart

## Prerequisites Details
* Kubernetes 1.8+
* PV support on the underlying infrastructure

## Chart Details
This chart will do the following:

* Instantiate 3 instances of OpenLDAP server with multi-master replication
* A phpldapadmin to administrate the OpenLDAP server
* ltb-passwd for self service password

## Installation

To install the chart with the release name `openldap`:

```bash
$ helm install openldap helm-openldap/openldap-stack-ha .
```

## Configuration

We use the docker images provided by https://github.com/osixia/docker-openldap. The docker image is highly configurable and well documented. Please consult to documentation for the docker image for more information.

The following table lists the configurable parameters of the openldap chart and their default values.

| Parameter                          | Description                                                                                                                               | Default             |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------- |
| `replicaCount`                     | Number of replicas                                                                                                                        | `3`                 |
| `updateStrategy`                   | StatefulSet update strategy                                                                                                               | `{}`                |
| `openldap.imagerepository`                 | Container image repository                                                                                                                | `osixia/openldap`   |
| `openldap.imagetag`                        | Container image tag                                                                                                                       | `openldap.11.10`            |
| `openldap.imagepullPolicy`                 | Container pull policy                                                                                                                     | `IfNotPresent`      |
| `extraLabels`                      | Labels to add to the Resources                                                                                                            | `{}`                |
| `podAnnotations`                   | Annotations to add to the pod                                                                                                             | `{}`                |
| `existingSecret`                   | Use an existing secret for admin and config user passwords                                                                                | `""`                |
| `openldap.serviceannotations`              | Annotations to add to the service                                                                                                         | `{}`                |
| `openldap.serviceexternalIPs`              | Service external IP addresses                                                                                                             | `[]`                |
| `openldap.serviceldapPort`                 | External service port for LDAP                                                                                                            | `389`               |
| `openldap.serviceldapPortNodePort`                 | Nodeport of External service port for LDAP if service.type is NodePort                                                                                                            | `nil`               |
| `openldap.serviceloadBalancerIP`           | IP address to assign to load balancer (if supported)                                                                                      | `""`                |
| `openldap.serviceloadBalancerSourceRanges` | List of IP CIDRs allowed access to load balancer (if supported)                                                                           | `[]`                |
| `openldap.servicesslLdapPort`              | External service port for SSL+LDAP                                                                                                        | `636`               |
| `openldap.servicesslLdapPortNodePort`                 | Nodeport of External service port for SSL if service.type is NodePort                                                                                                            | `nil`               |
| `openldap.servicetype`                     | Service type can be ClusterIP, NodePort, LoadBalancer                                                                                                                              | `ClusterIP`         |
| `openldap.env`                              | List of key value pairs as env variables to be sent to the docker image. See https://github.com/osixia/docker-openldap for available ones | `[see values.yaml]` |
| `openldap.logLevel`                         | Set the container log level. Valid values: `none`, `error`, `warning`, `info`, `debug`, `trace`                                           | `info`              |
| `openldap.customTLSenabled`                      | Set to enable TLS/LDAPS with custom certificate - should also set `openldap.tlssecret`                                                                                    | `false`             |
| `openldap.customTLSsecret`                       | Secret containing TLS cert and key must contain the keys tls.key , tls.crt and ca.crt (if tls.CA.enabled: true)                                                                       | `""`                |
| `openldap.customTLSCA.enabled`                   | Set to enable custom CA crt file                                                                         | `false`             |
| `openldap.adminPassword`                    | Password for admin user. Unset to auto-generate the password                                                                              | None                |
| `openldap.configPassword`                   | Password for config user. Unset to auto-generate the password                                                                             | None                |
| `openldap.customLdifFiles`                  | Custom ldif files to seed the LDAP server. List of filename -> data pairs                                                                 | None                |
| `openldap.persistenceenabled`              | Whether to use PersistentVolumes or not                                                                                                   | `false`             |
| `openldap.persistencestorageClass`         | Storage class for PersistentVolumes.                                                                                                      | `<unset>`           |
| `openldap.persistenceaccessMode`           | Access mode for PersistentVolumes                                                                                                         | `ReadWriteOnce`     |
| `openldap.persistencesize`                 | PersistentVolumeClaim storage size                                                                                                        | `8Gi`               |
| `openldap.resources`                        | Container resource requests and limits in yaml                                                                                            | `{}`                |
| `openldap.testenabled`                     | Conditionally provision test resources                                                                                                    | `false`             |
| `openldap.testimage.repository`            | Test container image requires bats framework                                                                                              | `dduportal/bats`    |
| `openldap.testimage.tag`                   | Test container tag                                                                                                                        | `openldap.04.0`             |
| `openldap.replicationenabled`              | Enable the multi-master replication | `true` |
| `openldap.replicationretry`              | retry period for replication in sec | `60` |
| `openldap.replicationtimeout`              | timeout for replication  in sec| `1` |
| `openldap.replicationstarttls`              | starttls replication | `critical` |
| `openldap.replicationtls_reqcert`              | tls certificate validation for replication | `never` |
| `openldap.replicationinterval`              | interval for replication | `00:00:00:10` |
| `openldap.replicationclusterName`          | Set the clustername for replication | "cluster.local" |
| `phpldapadmin.enabled`             | Enable the deployment of PhpLdapAdmin | `true`|
| `phpldapadmin.ingress`             | Ingress of Phpldapadmin | `{}` |
| `phpldapadmin.env`  | Environment variables for PhpldapAdmin| `{}` |
|`ltb-passwd.enabled`| Enable the deployment of Ltb-Passwd| `true` |
|`ltb-passwd.ingress`| Ingress of the Ltb-Passwd service | `{}` |
|`ltb-passwd.ldap`| Ldap configuration for the Ltb-Passwd service | `{}` |
|`ltb-passwd.env`| Environment variables for ltp-passwd | `{}` |

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name openldap -f values.yaml stable/openldap .
```

## PhpLdapAdmin
To enable PhpLdapAdmin set `openldap.phpldapadminenabled`  to `true`

Ingress can be configure if you want to expose the service.
Setup the env part of the configuration to access the OpenLdap server

**Note** : The ldap host should match the following `openldap.namespaceAppfullname`

Example : 
```
phpldapadmin:
  enabled: true
  ingress:
    enabled: true
    annotations: {}
    path: /
    ## Ingress Host
    hosts:
    - phpldapadmin.local
  env:
    PHPLDAPADMIN_LDAP_HOSTS: openldap.openldap
     
```
## Self-service-password
To enable Self-service-password set `ltb-passwd.enabled`  to `true`

Ingress can be configure if you want to expose the service.

Setup the `ldap` part with the information of the OpenLdap server.

Set `bindDN` accordingly to your ldap domain

**Note** : The ldap server host should match the following `ldap://namespace.Appfullname`

Example : 
```
ltb-passwd:
  enabled : true
  ingress:
    enabled: true
    annotations: {}
    host: "ssl-ldap2.local"
  ldap:
    server: ldap://openldap.openldap
    searchBase: dc=example,dc=org
    bindDN: cn=admin,dc=example,dc=org
    bindPWKey: LDAP_ADMIN_PASSWORD
  
```

## Cleanup orphaned Persistent Volumes

Deleting the Deployment will not delete associated Persistent Volumes if persistence is enabled.

Do the following after deleting the chart release to clean up orphaned Persistent Volumes.

```bash
$ kubectl delete pvc -l release=${RELEASE-NAME}
```

## Custom Secret

`existingSecret` can be used to override the default secret.yaml provided
