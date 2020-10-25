# CFMR installation preparation guide

Before you can install cfmr, you must configure an environment file that defines environment variables for the installation. You can also configure LDAP integration for your cfmr installation in the environment file.


To generate this environment file, we have to create a file as `<your-environment-name>.env.list` and save it.

## Basic installation preparation

To prepare for basic installation, configure any of the following values that apply to your installation in your `<your-environment-name>.env.list` file:

```
# Env list for cfmr installation 
OPENSHIFT_URL=<The server that is also provided by IBM Cloud OpenShift, for example, https://<something>.cloud.ibm.com:<port>>
OPENSHIFT_TOKEN=<Openshift cluster token string, for example, <alphanumberic long token>
MULTI_ENVIRONMENTS=<If you added features.multiEnvironments value as true in configuration file then provide space separated environment names like dev test prod etc. If its value is false then leave it blank >

```

## LDAP integration preparation

If you added features.enableLdapIntegration value as true in above configuration i.e. values.yaml, then configure the following values in your `<your-environment-name>.env.list` file as well:

```
# Env list for UAA-LDAP integration
# Mark the LDAP_INTEGRATION_REQ to true if you need LDAP to be integrated with CF and OpenShift
# for authentication and authorization purposes
LDAP_INTEGRATION_REQ=false
# If LDAP_INTEGRATION_REQ is set to true, please provide the below information
LDAP_IP_ADDRESS=<The IP address where the LDAP server is running.>
LDAP_PORT=<The Port for the LDAP  server>
LDAP_USER_DN=<Entire LDAP admin CN information here - Example: cn=LDAPadmin,dc=..,dc=..>
LDAP_USER_PWD=<LDAP password>
LDAP_SEARCH_BASE=<Main base DN information>
LDAP_SEARCH_FILTER=<LDAP search filter for UAA to search user information - Example: uid{0}>
LDAP_GROUP_SEARCH_FILTER=<The LDAP group search filter for UAA to search for member users in a group - Example: memberUid{0}>
LDAP_GROUP_SEARCH_BASE=<LDAP group search base - the base DN to start the group search>
LDAP_CC_ADMIN_FILTER=<CN for cloudcontroller.admin group - This is for UAA to add this as a group for external users>
# Provide below information to integrate OpenShift with LDAP
LDAP_ATTR_ID=<base attribute to map - Example: dn>
LDAP_ATTR_NAME=<name attribute to map - Example: cn>
LDAP_ATTR_PRFRD_USER_NAME=<preferred user name to map - Example: uid
LDAP_BIND_DN=<LDAP bind DN - Leave blank if search can be performed without a bind_dn>
LDAP_BIND_PWD=<LDAP bind password - Leave blank if search can be performed without a bind_dn - Cannot be null if bind_dn is provided>
LDAP_FILTER_PARAMS=<LDAP filter parameters for OpenShift to search for users - Example: ou=users,dc=..,dc=..?uid>

```
## How to pass this env in helm install ?????

### Running an Install

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

### Running an Install for multi environment setup with ldap integration in a cluster (If features.multiEnvironments and features.enableLdapIntegration values are true)


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

