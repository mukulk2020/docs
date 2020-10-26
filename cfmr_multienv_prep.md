# Cloud Foundry Migration Runtime 
IBM Cloud Foundry Migration Runtime is a Cloud Foundry platform built to run on Red Hat OpenShift. 

## Introduction
This is a helm chart to install Cloud Foundry Migration Runtime.

## Details

IBM Cloud Foundry Migration Runtime gives developers the productivity from Cloud Foundry and allows platform operators to manage the infrastructure abstraction with OpenShift tools and APIs.


## Prerequisites

### Supported OpenShift versions and platforms
You can install your product on Linux® x86_64, Linux® with a supported version of OpenShift Container Platform.

Table 1. Supported OpenShift versions

|Platform|OpenShift Container Platform versions|
|--------|-------------------------------------|
|Linux® x86_64|4.3.12 and above, 4.4.3 and above|

## Resources Required

### Cluster Sizing
A OpenShift cluster to support a basic workload will require:

|         |Master Nodes|Worker Nodes|vCPUs per node|RAM per node|
|---------|------------|------------|--------------|------------|
|Minimal  | 3*         |3	        |4	           |16GB        |
|Preferred|	3*         |3	        |8	           |32GB        |

* In some configurations such as ROKS on IBM Cloud OpenShift nodes can have duel master and worker nodes.

### Storage Requirements

#### `cfmr` Persistent Volumes
There are two Persistent Volumes created for the cfmr project.
* First persistent volume is created with access mode of RWO and with a capacity of 20Gi. The reclaim policy for this persistent volume is Delete, status as Bound and it’s persistent volume claim name is pxc-data-database-0
* Second persistent volume is created with access mode of RWO and with a capacity of 100Gi. The reclaim policy for this persistent volume is Delete, status as Bound and it’s persistent volume claim name is singleton-blobstore-pvc-singleton-blobstore-0

#### `cfmr-ui` Persistent Volumes
There is only one Persistent Volume created for the cfmr-ui project with access mode of RWO and with a capacity of 20Gi. The reclaim policy for this PV is Delete, status as Bound and it's persistent volume claim name is console-mariadb

## PodSecurityPolicy Requirements
The predefined PodSecurityPolicy name [`ibm-restricted-psp`](https://ibm.biz/cpkspec-psp) has been verified for this chart. If your target namespace is bound to this PodSecurityPolicy, you can proceed to install the chart.

## SecurityContextConstraints Requirements
This chart requires adding `restricted`, `cluster-admin`, `self-provisioner` policy to service account `ibm-cfmr-serviceaccount` in `ibm-cfmr` name space.

* From the user interface, you can copy and paste the following snippets to enable the custom `SecurityContextConstraints`
  * Custom SecurityContextConstraints definition:

  ```yaml
  apiVersion: security.openshift.io/v1
  kind: SecurityContextConstraints
  metadata:
    annotations:
    name: ibm-cfmr-prod-scc
  allowHostDirVolumePlugin: false
  allowHostIPC: false
  allowHostNetwork: false
  allowHostPID: false
  allowHostPorts: false
  allowPrivilegedContainer: false
  allowedCapabilities: []
  allowedFlexVolumes: []
  defaultAddCapabilities: []
  defaultPrivilegeEscalation: false
  forbiddenSysctls:
    - "*"
  fsGroup:
    type: MustRunAs
    ranges:
    - max: 65535
      min: 1
  readOnlyRootFilesystem: false
  requiredDropCapabilities:
  - ALL
  runAsUser:
    type: MustRunAsNonRoot
  seccompProfiles:
  - docker/default
  seLinuxContext:
    type: RunAsAny
  supplementalGroups:
    type: MustRunAs
    ranges:
    - max: 65535
      min: 1
  volumes:
  - configMap
  - downwardAPI
  - emptyDir
  - persistentVolumeClaim
  - projected
  - secret
  priority: 0
  ```
  
## Installing
Installation of Cloud Foundry Migration Runtime is performed using helm.

### Configuration
| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `global.image.repository` | Global image repository. Overrides `image.repository` | `` |
| `global.image.pullSecret` | Global image pull secret. | `` |
| `nameOverride` | Changes the name of created kubernetes objects after the the release name. If empty (the default value) the ibm-cfmr-installer is used | `` |
| `image.repository` | Docker repository | `ibm-cfmr-installer` |
| `image.tag` | Docker image tag | `1.0.0` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `action` | Action to perform by installer. Available actions are `install`, `check`, `verify` | `install` |
| `features.cfoVersion` | CF-Operator version | `` |
| `features.kubecfVersion` | KubeCF version | `` |
| `features.stratosVersion` | Stratos version | `` |
| `features.nginxVersion` | NGINX Ingress version | `` |
| `features.storageClass` | Specified name of storage class | `` |
| `features.enableCertManager` | Enable certificate manager for secure connections to UI, cf api, and applications deployed. | `false` |
| `features.enableHighAvailability` | Enable highly available installation of cfmr | `false` |
| `features.enableLdapIntegration` | Enable LDAP integration with cfmr. Valid values are true or false | `false` |
| `features.multiEnvironments` | Enabled multiple environments to be setup in a single cluster. For example, `dev test` will create two environments `dev-cfmr` and `test-cfmr`, if left blank only one instance will be created in the OpenShift cluster | `` |
| `resources.requests.cpu` | CPU request | `1000m` |
| `resources.requests.memory` | Memory request | `2Gi` |
| `resources.limits.cpu` | CPU limit | `2000m` |
| `resources.limits.memory` | Memory limit | `4Gi` |
| `rbac.create` | Create roles and bind to created etcd service account | `true` |
| `rbac.existingServiceAccount` | Name of existing service account to use | `` |

### What is Installed?

#### Namespaces
Cloud Foundry Migration Runtime uses five namespaces (OpenShift projects). By default these start with the prefix `cfmr`.

|Namespace                                 |Purpose|
|------------------------------------------|-------|
|cfmr                                      |Contains Cloud Foundry system components|
|cfmr-cf-operator                          |Operator for managing the system deployed to cfmr|
|cfmr-eirini                               |Contains user applications deployed by the system installed in cfmr|
|cfmr-ingress-nginx                        |Nginx Ingress used by Cloud Foundry Migration Runtime to manage routing to both the Cloud Foundry system in cfmr, UI in cfmr-ui and user applications in cfmr-eirini|
|cfmr-ui                                   |Cloud Foundry Migration Runtime UI components|

If the optional cert-manager is also installed there is also a cert-manager namespace created.

|Namespace                                 |Purpose|
|------------------------------------------|-------|
|cert-manager                              |Certificate manager that issues TLS certificates to Cloud Foundry Migration Runtime|

## Installation

### Basic Install

```shell script
helm install ibm-cfmr-installer \
  --values values.yaml \
  --path charts/ibm-cfmr-prod \
  --set "OPENSHIFT_URL=<The server that is also provided by IBM Cloud OpenShift, for example, https://<something>.cloud.ibm.com:<port>>" \
  --set "OPENSHIFT_TOKEN=<Openshift cluster token string, for example, <alphanumberic long token>"
```
### Running an Install for multi environment setup in a cluster (If features.multiEnvironments value is true)

```shell script
helm install ibm-cfmr-installer \
  --values values.yaml \
  --path charts/ibm-cfmr-prod \
  --set "OPENSHIFT_URL=<The server that is also provided by IBM Cloud OpenShift, for example, https://<something>.cloud.ibm.com:<port>>" \
  --set "OPENSHIFT_TOKEN=<Openshift cluster token string, for example, <alphanumberic long token>" \
  --set "MULTI_ENVIRONMENTS=<Provide space separated environment names like dev test prod etc. If features.multiEnvironments value is false then leave it blank >"
```

### Running an Install for multi environment setup with ldap integration in a cluster (If features.multiEnvironments and features.enableLdapIntegration both values are true)


```shell script
helm install ibm-cfmr-installer \
  --values values.yaml \
  --path charts/ibm-cfmr-prod \
  --set "OPENSHIFT_URL=<The server that is also provided by IBM Cloud OpenShift, for example, https://<something>.cloud.ibm.com:<port>>" \
  --set "OPENSHIFT_TOKEN=<Openshift cluster token string, for example, <alphanumberic long token>" \
  --set "MULTI_ENVIRONMENTS=<Provide space separated environment names like dev test prod etc. If features.multiEnvironments value is false then leave it blank >" \
  --set "LDAP_INTEGRATION_REQ=true" \
  --set "LDAP_IP_ADDRESS=<The IP address where the LDAP server is running.>" \
  --set "LDAP_PORT=<The Port for the LDAP  server>" \
  --set "LDAP_USER_DN=<Entire LDAP admin CN information here - Example: cn=LDAPadmin,dc=..,dc=..>" \
  --set "LDAP_USER_PWD=<LDAP password>" \
  --set "LDAP_SEARCH_BASE=<Main base DN information>" \
  --set "LDAP_SEARCH_FILTER=<LDAP search filter for UAA to search user information - Example: uid{0}>" \
  --set "LDAP_GROUP_SEARCH_FILTER=<The LDAP group search filter for UAA to search for member users in a group - Example: memberUid{0}>" \
  --set "LDAP_GROUP_SEARCH_BASE=<LDAP group search base - the base DN to start the group search>" \
  --set "LDAP_CC_ADMIN_FILTER=<CN for cloudcontroller.admin group - This is for UAA to add this as a group for external users>" \
  --set "LDAP_ATTR_ID=<base attribute to map - Example: dn>" \
  --set "LDAP_ATTR_NAME=<name attribute to map - Example: cn>" \
  --set "LDAP_ATTR_PRFRD_USER_NAME=<preferred user name to map - Example: uid" \
  --set "LDAP_BIND_DN=<LDAP bind DN - Leave blank if search can be performed without a bind_dn>" \
  --set "LDAP_BIND_PWD=<LDAP bind password - Leave blank if search can be performed without a bind_dn - Cannot be null if bind_dn is provided>" \
  --set "LDAP_FILTER_PARAMS=<LDAP filter parameters for OpenShift to search for users - Example: ou=users,dc=..,dc=..?uid>"

```

## Limitations

## Documentation

## Copyright

© Copyright IBM Corporation 2020. All Rights Reserved.

