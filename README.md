<!--

    Sonatype Nexus (TM) Open Source Version
    Copyright (c) 2017-present Sonatype, Inc.
    All rights reserved. Includes the third-party code listed at http://links.sonatype.com/products/nexus/oss/attributions.

    This program and the accompanying materials are made available under the terms of the Eclipse Public License Version 1.0,
    which accompanies this distribution and is available at http://www.eclipse.org/legal/epl-v10.html.

    Sonatype Nexus (TM) Professional Version is available from Sonatype, Inc. "Sonatype" and "Sonatype Nexus" are trademarks
    of Sonatype, Inc. Apache Maven is a trademark of the Apache Software Foundation. M2eclipse is a trademark of the
    Eclipse Foundation. All other trademarks are the property of their respective owners.

-->
# Nexus Repository R Format

[![CircleCI](https://circleci.com/gh/sonatype-nexus-community/nexus-repository-r.svg?style=svg)](https://circleci.com/gh/sonatype-nexus-community/nexus-repository-r) [![Join the chat at https://gitter.im/sonatype/nexus-developers](https://badges.gitter.im/sonatype/nexus-developers.svg)](https://gitter.im/sonatype/nexus-developers?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) [![DepShield Badge](https://depshield.sonatype.org/badges/sonatype-nexus-community/nexus-repository-r/depshield.svg)](https://depshield.github.io)
[![Maven Central](https://img.shields.io/maven-central/v/org.sonatype.nexus.plugins/nexus-repository-r.svg?label=Maven%20Central)](https://search.maven.org/search?q=g:%22org.sonatype.nexus.plugins%22%20AND%20a:%22nexus-repository-r%22)

# Table Of Contents
* [Developing](#developing)
   * [Requirements](#requirements)
   * [Building](#building)
* [Using R with Nexus Repository Manager 3](#using-r-with-nexus-repository-manager-3)
* [Installing the plugin](#installing-the-plugin)
   * [Temporary Install](#temporary-install)
   * [(more) Permanent Install](#more-permanent-install)
   * [(most) Permament Install](#most-permanent-install)
* [The Fine Print](#the-fine-print)
* [Getting Help](#getting-help)

## Developing

### Contribution Guidelines

Go read [our contribution guidelines](/.github/CONTRIBUTING.md) to get a bit more familiar with how
we would like things to flow.

### Requirements

* [Apache Maven 3.3.3+](https://maven.apache.org/install.html)
* OpenJDK 8
* Network access to https://repository.sonatype.org/content/groups/sonatype-public-grid

Also, there is a good amount of information available at [Bundle Development](https://help.sonatype.com/display/NXRM3/Bundle+Development+Overview)

### Building

To build the project and generate the bundle use Maven

    mvn clean install

If everything checks out, the bundle for R should be available in the `target` folder

## Using R With Nexus Repository Manager 3

After installing the plugin, we have detailed instructions on [how to get started here!](docs/R_USER_DOCUMENTATION.md)

## Compatibility with Nexus Repository Manager 3 Versions

The table below outlines what version of Nexus Repository the plugin was built against

| Plugin Version | Nexus Repository Version |
|----------------|--------------------------|
| v1.0.0         | <3.8.0-02                |
| v1.0.1         | >=3.8.0-02               |
| v1.0.2         | >=3.14.0-04              |
| v1.0.3         | >=3.15.2-01              |
| v1.0.4         | >=3.18.0-01              |

If a new version of Nexus Repository is released and the plugin needs changes, a new release will be made, and this
table will be updated to indicate which version of Nexus Repository it will function against. This is done on a time 
available basis, as this is community supported. If you see a new version of Nexus Repository, go ahead and update the
plugin and send us a PR after testing it out!

All released versions can be found [here](https://github.com/sonatype-nexus-community/nexus-repository-r/releases).

## Installing the plugin

There are a range of options for installing the R plugin. You'll need to build it first, and
then install the plugin with the options shown below:

### Temporary Install

Installations done via the Karaf console will be wiped out with every restart of Nexus Repository. This is a
good installation path if you are just testing or doing development on the plugin.

* Enable Nexus console: edit `<nexus_dir>/bin/nexus.vmoptions` and change `karaf.startLocalConsole`  to `true`.

  More details here: https://help.sonatype.com/display/NXRM3/Installing+Bundles

* Run NXRM console mode (the below assumes you are running as 'nexus' user as recommended in our documentation) 
and enter the bundle command after it has started:
  ```
  # sudo su - nexus
  $ cd <nexus_dir>/bin
  $ ./nexus run
  > bundle:install file:///tmp/nexus-repository-r-1.0.3.jar
  ```
  This will return text similar to "Bundle ID: 282".  You can double check this ID using bundle:list 
  (for org.sonatype.nexus.plugins:nexus-repository-r; it should be the last one).  Then start it using this 
  bundle command:
  ```
  > bundle:start <org.sonatype.nexus.plugins:nexus-repository-r ID>
  ```

### (more) Permanent Install

For more permanent installs of the nexus-repository-r plugin, follow these instructions:

* Copy the bundle (nexus-repository-r-1.0.3.jar) into <nexus_dir>/deploy

This will cause the plugin to be loaded with each restart of Nexus Repository. As well, this folder is monitored
by Nexus Repository and the plugin should load within 60 seconds of being copied there if Nexus Repository
is running. You will still need to start the bundle using the karaf commands mentioned in the temporary install.

### (most) Permanent Install

If you are trying to use the R plugin permanently, it likely makes more sense to do the following:

* Copy the bundle into `<nexus_dir>/system/org/sonatype/nexus/plugins/nexus-repository-r/1.0.3/nexus-repository-r-1.0.3.jar`
* If you are using OSS edition, make these mods in: `<nexus_dir>/system/com/sonatype/nexus/assemblies/nexus-oss-feature/3.x.y/nexus-oss-feature-3.x.y-features.xml`
* If you are using PRO edition, make these mods in: `<nexus_dir>/system/com/sonatype/nexus/assemblies/nexus-pro-feature/3.x.y/nexus-pro-feature-3.x.y-features.xml`
   ```
         <feature version="3.x.y.xy" prerequisite="false" dependency="false">nexus-repository-rubygems</feature>
   +     <feature version="1.0.3" prerequisite="false" dependency="false">nexus-repository-r</feature>
         <feature version="3.x.y.xy" prerequisite="false" dependency="false">nexus-repository-yum</feature>
     </feature>
   ```
   And
   ```
   + <feature name="nexus-repository-r" description="org.sonatype.nexus.plugins:nexus-repository-r" version="1.0.3">
   +     <details>org.sonatype.nexus.plugins:nexus-repository-r</details>
   +     <bundle>mvn:org.sonatype.nexus.plugins/nexus-repository-r/1.0.3</bundle>
   + </feature>
    </features>
   ```
This will cause the plugin to be loaded and started with each startup of Nexus Repository.

## The Fine Print

It is worth noting that this is **NOT SUPPORTED** by Sonatype, and is a contribution of ours
to the open source community (read: you!)

Remember:

* Use this contribution at the risk tolerance that you have
* Do NOT file Sonatype support tickets related to R support
* DO file issues here on GitHub, so that the community can pitch in

Phew, that was easier than I thought. Last but not least of all:

Have fun creating and using this plugin and the Nexus platform, we are glad to have you here!

## Getting help

Looking to contribute to our code but need some help? There's a few ways to get information:

* Chat with us on [Gitter](https://gitter.im/sonatype/nexus-developers)
* Check out the [Nexus3](http://stackoverflow.com/questions/tagged/nexus3) tag on Stack Overflow
* Check out the [Nexus Repository User List](https://groups.google.com/a/glists.sonatype.com/forum/?hl=en#!forum/nexus-users)
