---

copyright:
years: 2020
lastupdated: "2020-10-21"

---

{:new_window: target="blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:child: .link .ulchildlink}
{:childlinks: .ullinks}

# {{site.data.keyword.cfmr_name}} installation preparation guide

Before you can install {{site.data.keyword.cfmr_short}}, you must configure an environment file that defines environment variables for the installation. You can also configure LDAP integration for your {{site.data.keyword.cfmr_short}} installation in the environment file.
{:shortdesc}

To generate this environment file, make a copy of the `env.list.template` file in the `environments` directory and save it as `<your-environment-name>.env.list`.

## Basic installation preparation

To prepare for basic installation, configure any of the following values that apply to your installation in your `<your-environment-name>.env.list` file:

```
# Env list for cfmr installation (single env or multi env)
OPENSHIFT_URL=<The server that is also provided by IBM Cloud OpenShift, for example, https://<something>.cloud.ibm.com:<port>>
OPENSHIFT_TOKEN=<Openshift cluster token string, for example, <alphanumberic long token>
MULTI_ENVIRONMENTS= <multiple environments to be created in a single cluster. Provide space separated environment names like dev test prod etc., If left blank only one instance of {{site.data.keyword.cfmr.name}} will be created in the OpenShift cluster>

```

## LDAP integration preparation

If you need to [integrate the Cloud Foundry User Account and Authentication (UAA) server with an LDAP server](../config_manager/cfmr_rbac_uaa_integration.md), configure the following values in your `<your-environment-name>.env.list` file:

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

## Next steps
After you configure these parameters as needed, you can [install {{site.data.keyword.cfmr_short}} with the runtime install tools](./cfmr_install.md).
