# Installer for {{site.data.keyword.cfmr.name}}

[![container image](https://images.microbadger.com/badges/version/hclcnlabs/cf4ocp-installer.svg)](https://microbadger.com/images/hclcnlabs/cf4ocp-installer)
[![IBM Build Status](https://travis.ibm.com/CFMigrationRuntime/cf4ocp-installer.svg?token=TL9aP6Bi1VWkadLGjpvs&branch=master)](https://travis.ibm.com/CFMigrationRuntime/cf4ocp-installer)

This is an installer for Cloud Foundry for OpenShift and is intended to run in an OpenShift cluster or with a connection to an OpenShift cluster.

## Usage

See available commands:

```shell script
docker run {{site.data.keyword.cfmr.docker_repo}}/{{site.data.keyword.cfmr.name}}-installer <command>
```

Run a command e.g. install

```shell script
docker run {{site.data.keyword.cfmr.docker_repo}}/{{site.data.keyword.cfmr.name}}-installer install
```

Run a command e.g. check with an environment configuration

```shell script
docker run --env-file=./environments/ocp43-vc1.env.list {{site.data.keyword.cfmr.docker_repo}}/{{site.data.keyword.cfmr.name}}-installer check
```

To run in interactive mode use debug

```shell script
docker run {{site.data.keyword.cfmr.docker_repo}}/{{site.data.keyword.cfmr.name}}-installer debug
```

## Extended

There is also an extended version of the {{site.data.keyword.cfmr.name}}-installer that has extra debugging and developer options.

To run in this in interactive mode use debug

```shell script
docker run {{site.data.keyword.cfmr.docker_repo}}/{{site.data.keyword.cfmr.name}}-installer-extended debug
```

# {{site.data.keyword.product}} Verify Installs

To verify if a {{site.data.keyword.product}} install is successful, execute the following command:

```docker
docker run {{site.data.keyword.docker_repo}}/{{site.data.keyword.cfmr.name}}-installer verify
```

This command does the following:

* Logs into the OpenShift environment
* Verifies if {{site.data.keyword.product}} is running successfully
* Checks if the url for cf api exists
