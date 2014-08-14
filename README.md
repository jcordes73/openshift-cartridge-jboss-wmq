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

```bash
oo-admin-cartridge -a install -s /usr/libexec/openshift/cartridges/jboss-wmq
```

The last installation step is to clear the caches on the OpenShift Broker:

```bash
oo-admin-broker-cache --clear --console
```

## Remove

To remove the cartridge you first need to remove the package installed via RPM:

```bash
rpm -e openshift-cartridge-jboss-wmq-${project.version}-1.noarch
```

The next command will list all available OpenShift cartridges along with it's versions and cartridge-versions:

```bash
oo-admin-cartridge -l
```

Finally you delete the cartridge using this command

```bash
oo-admin-cartridge -a erase --name jbosswmq --version ${wmq.version} --cartridge_version ${project.version}
```

## Cartridge creation

When creating the cartridge you can pass the following environment variables to override the defaults specified in the pom.xml earlier:

|Name|Default (pom.xml)|
|----|------------------|
|OPENSHIFT_JBOSSWMQ_JNDI_NAME|wmq.jndiName|
|OPENSHIFT_JBOSSWMQ_CONNECTIONNAMELIST|wmq.connectionNameList|
|OPENSHIFT_JBOSSWMQ_CHANNEL|wmq.channel|
|OPENSHIFT_JBOSSWMQ_QUEUE_MANAGER|wmq.queueManager|
