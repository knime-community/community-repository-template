# Template for community extension repositories

## Introduction
This repository contains the necessary example configuration for building KNIME
AP extensions with maven.

## Converting an existing buckminster build to use maven
If you have a repository that is currently being build with buckminster and you
want to convert it to maven, take a look at the instructions in [this
guide](https://github.com/knime-community/community-repository-template/blob/master/Converting_from_Buckminster.md).

### Example configurations
In addition to plug-ins and features that are meant to be installed by the
users of an extension, every repository needs some additional files and folders, 
to facilitate the build process.

* An update site project for specifying which artifacts should be built and published.
  `org.knime.community.template.update` is the template for this. Its `category.xml` contains a list of all features
  (and optionally plug-ins/bundles), including potential source features (or bundles). 
* `pom.xml`: It lists all modules/projects that should be processed during the build as well as all p2
  repositories that contain required dependencies.

## How to build and install
You need to have maven installed in at least version `3.6.3`, then you can
build using `mvn verify`. This will compile the code, run the tests and
create the update site. You will find the update site in the directory
`org.knime.community.template.update/target/repository`, you can add this
path to your AP and install the freshly build plugins for local debugging.

## Example projects explained:
This repo contains several example projects that demonstrate certain features 
- `org.knime.community.template.feature` Example feature project.
- `org.knime.community.template.plugin` Example plugin project
- `org.knime.community.template.tests` Example unit test plugin, tests
contained in this project are run in a minimal eclipse rcp instance,
generated using your plug-in dependencies. This ensures they can access all
the necessary dependencies.
- `org.knime.community.template.update` Example update site project
- `org.knime.community.template.no-source-files` this project demonstrates
the special case for plugins that do not contain source file. Maven will
complain about this, the `pom.xml` contains a section (lines 127-147) where
such projects are excluded