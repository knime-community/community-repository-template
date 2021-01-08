# Converting an existing multi-feature/multi-plugin build from Buckminster

These instructions assume you have multiple features and plugins in a single git repo - start by creating a new branch so you can trash it and start again if you need to!

A helper KNIME workflow can be found on the hub at https://kni.me/w/y1XA6gYHUj4BY1ql

## Parent Project
*  Create a parent project as follows:
  1. In Eclipse, File -> New -> Project...
  2. General -> Project
  3. Give the project a sensible name (e.g. `org.foo.bah.parent`)
  4. Uncheck the 'Use default location' option, and browse to the top level of your git repo holding all your existing plugins / features
  5. You can also add your new 'parent' project to any working sets in eclipse at this point
  6. Click 'Finish'
  7. If you open the new project in the Package explorer view you will now see a project comprising a `.project` file, a `.gitignore` file if you have one in your repo, and multiple folders which contain the nested projects (Eclipse doesnt play nicely with nested projects - c'est la vie!)
*  Copy the `.mvn` folder and `pom.xml` file from https://github.com/knime-community/community-repository-template to your new project root directory (*TIP*: In Eclipse, if you Right-click on the project in the package explorer view and select 'Properties', in the 'Resource' panel in the resulting dialog, clicking the button at the end of the line `Location:` takes you to the project's root folder in the system file browser)
*  In Eclipse, edit the `pom.xml` file in the parent project:
  1. Replace the contents of the `<modules>` tag with all your included projects
    - You can probably get the features from the `feature.xml` file in your buckminster site feature project, and use a find/replace (Find: `<includes\s+id=\"(.+?)\"\s+version="0.0.0"/>` regular expression, Replace with: `<module>$1</module>`), or use the helper workflow.
    - The plugins and test fragments you will need to either list manually or you could use the helper KNIME workflow to parse all the `feature.xml` files for included plugins and features
  2. Edit the `<repositories>` tag contents with the repositories needed for your dependencies (You probably have these already in your `buckminster.rmap` file in your site feature project from your buckminster build, in which case the helper KNIME Workflow will generate this block for you and you just need to paste it in)
  3. Change the `<groupId>` and `<artifactId>` tags to whatever you use
  4. List any no-source plugins in the `<plugins>` configuration block where indicated in the comments.

## 'Site' Project
If you have a feature project defining your update site in the `build.properties` file with something like:
```
category.id.org.foo.bah.category=Foo Bah!
category.members.org.foo.bah.category=org.foo.feature,\
	org.bah.feature
category.description.foo.bah.category=Features providing Foo Bah functionality
```
along with a `buckminster.rmap` file and `feature.xml` file then you need to either convert it to an update site feature in which the site is defined in a `category.xml` file, or create a new 'update' project based on the example at https://github.com/knime-community/community-repository-template/tree/master/org.knime.community.template.update. You no longer need any of the `buckminster.*` or `feature.xml` files once you have completed the conversion process.

### Converting a Buckminster 'site' feature to a maven update site projects
1. `.project` file.  This needs either:
  - Updating so that the build command `<name>` tag contains `org.eclipse.m2e.core.maven2Builder` and the `<nature>` tag contains `org.eclipse.m2e.core.maven2Nature`

  or:
  - Replacing with the `.project` file from the example repository, in which case the project name needs to be replaced with your existing project name

2. `category.xml` file - automatic approach.  The helper KNIME workflow will parse the `feature.xml` and the `build.properties` files of the 'site' project and write (WARNING: It will also overwrite without warning) the corresponding `category.xml` file into the project folder

3. `category.xml` file - manual approach. Copy the template from https://github.com/knime-community/community-repository-template/blob/master/org.knime.community.template.update/category.xml into your project and modify it based on the contents of your `feature.xml` and `build.properties` files.
  - There is an (incomplete) description of the `category.xml` format at https://wiki.eclipse.org/Tycho/category.xml
  - Categories are defined with `<category-def>` tags.
  - Features are added to categories with a `<feature>` tag and nested `<category>` tags.
  - Any plugins in the directly listed in the `feature.xml` file of the original update site project should be added in `<bundle>` tags.  These are usually 'core'-type plugins required by one or more feautres, but not included in any of them.
  - It is important to ensure the `category.xml` file does not contain a `url` attribute, and that the version attribute is set as `version="0.0.0"`
  - It may be possible to generate the `category.xml` file programmatically in KNIME from the `build.properties` file.

4. `pom.xml` file
  - Copy the template from https://github.com/knime-community/community-repository-template/blob/master/org.knime.community.template.update/pom.xml into the site projects
  - Update the `<parent>` `<groupId>` and `<artifactId>` tags with the values in the parent project `pom.xml` file
  - Update the `<artifactId>` tag with the current update site project name

## Tests
Unit tests should be in fragments of the parent plugin, with the name ending `.tests`, following the template at https://github.com/sroughley/community-repository-template/tree/master/org.knime.community.template.tests.
Any existing test cases should have the `pom.xml` file from the above location added, and the `<parent>` `<groupId>` and `<artifactId>` tags updated to the values in the parent `pom.xml` file
