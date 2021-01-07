# Template for Analytics Platform repositories

In addition to plug-ins and features that are installed by user, every repository needs some additional files and folders:

* An update site project for specifying which artifacts should be built and published.
  `org.knime.update.ap-repository-template` is the template for this. Its `category.xml` contains a list of all features
  (and optionally plug-ins/bundles), including potential source features (or bundles). The `pom.xml` must be adapted to
  have the correct artifact ID.
* Optional but required in many cases: A "testing" feature that is installed as part of the workflow tests and pulls in
  everything that is required to run the test workflows. This includes the main feature in the repository, as well as
  dedicated test plug-ins and features from other projects that provide nodes which are used in the test workflows.
* Special files and configuration for test workflows in `workflow-tests`. Required VM arguments go into
  `workflow-test/vmargs`, required Eclipse preferences go into `workflow-tests/preferences.epf`.

  If you need OS-specific preferences, put them into `workflow-tests/preferences-Linux.epf`,
  `workflow-tests/preferences-Windows.epf`, or `workflow-tests/preferences-MacOSX.epf`, respectively. Only one
  preference file will be loaded, with the OS-specific file taking precedence.

  Any of those files can be deleted if they are not required.
* `pom.xml`: It lists all modules/projects that should be processed during the build as well as all upstream
  repositories that contain required dependencies.
* `Jenkinsfile`: used by the build system only. Have a look into the file for instructions what parts must be adapted.
