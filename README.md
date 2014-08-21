# openshift-cartridge-jboss-wmq

## Description

An OpenShift Enterprise 2.x cartridge for provisioning WebSphere MQ RAR on the JBoss EAP 6 cartridge.

## Adding WebSphere MQ RAR

First you have to download WebSphere MQ RAR from 


Copy the WebSphere MQ RAR into the project

```bash
cp wmq.jmsra.rar src/main/resources/cartridge/configuration
```

## Build

For building the project you need Maven installed. I have used Maven 3.0.5.

Just execute (after setting mvn on the PATH):

```bash
mvn clean install
```

Below you can find information on what properties are used and can be overriden by passing system properties (i.e. using -D{name})

|Property|Default|
|--------|-------|
|wmq.jndiName|java:jboss/eis/MQCF|
|wmq.connectionNameList|localhost(14138)|
|wmq.channel|MYCHANNEL|
|wmq.queueManager|MYQUEUEMANAGER|
|rpm.key.name|D3AABAF5|
|rpm.key.passphrase|redhat|

## Install

After the build you need to copy the resulting RPM to the target OpenShift node (for convenience I have used the /tmp directory):

```bash
scp target/rpm/openshift-cartridge-jboss-wmq/RPMS/noarch/openshift-cartridge-jboss-wmq-${project.version}-1.noarch.rpm root@${openshift.node.ip}:/tmp
```

then install it using

```bash
rpm -ivh openshift-cartridge-jboss-wmq-${project.version}-1.noarch.rpm
```

After the RPM has been installed successfully you need to install the cartridge like this:

###OSE 2.0

```bash
oo-admin-cartridge -a install -s /usr/libexec/openshift/cartridges/jboss-wmq
```

###OSE 2.1

```bash
oo-admin-ctl-cartridge -c import-node --activate
```

The last installation step is to clear the caches on the OpenShift Broker:

###OSE 2.0

```bash
oo-admin-broker-cache --clear --console
```

###OSE 2.1

```bash
service ruby193-mcollective restart
```

## Remove

To remove the cartridge you first need to remove the package installed via RPM:

```bash
rpm -e openshift-cartridge-jboss-wmq-${project.version}-1.noarch
```

The next command will list all available OpenShift cartridges along with it's versions and cartridge-versions:

###OSE 2.0
```bash
oo-admin-cartridge -l
```

###OSE 2.1

```bash
oo-admin-ctl-cartridge -c list
```

Finally you delete the cartridge using this command

###OSE 2.0

```bash
oo-admin-cartridge -a erase --name jbosswmq --version ${wmq.version} --cartridge_version ${project.version}
```

###OSE 2.1

```bash
oo-admin-ctl-cartridge -c delete -n jboss-wmq-${project.version}
```

## Cartridge creation

When creating the cartridge you can pass the following environment variables to override the defaults specified in the pom.xml earlier:

|Name|Default (pom.xml)|
|----|------------------|
|OPENSHIFT_JBOSSWMQ_JNDINAME|wmq.jndiName|
|OPENSHIFT_JBOSSWMQ_CONNECTIONNAMELIST|wmq.connectionNameList|
|OPENSHIFT_JBOSSWMQ_CHANNEL|wmq.channel|
|OPENSHIFT_JBOSSWMQ_QUEUEMANAGER|wmq.queueManager|
