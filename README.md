# gradle-multi-project
Making a multi-project with gradle and sharing build logic between subprojects


====

Let's say that all our projects will be Java projects.
In this case we want to apply a common set of rules to all of them, such as source directory layout, compiler flags,
code style conventions, code quality checks and so on.

Two out of three projects are more than just Java projects - they are libraries that we perhaps want to publish to an
external repository. Publishing configuration, such as a common group name for the libraries as well as the repository coordinates
might be a cross-cutting concern that both libraries need to share. For this example let's also say that we want to enforce that
our libraries expose some documentation with a common structure.

== Organizing build logic

From the use case above, we have identified that we have two types of projects - generic Java projects and public libraries.
We can model this use case by layering two separate plugins that each define the type of project that applies them:

----
├── buildSrc
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   ├── main
│   │   │   └── groovy
│   │   │       ├── myproject.java-conventions.gradle
│   │   │       └── myproject.library-conventions.gradle
...
----

* `myproject.java-conventions` - configures conventions that are generic for any Java project in the organization.
  It applies the core `java` and `checkstyle` plugins as well as an external `com.github.spotbugs` plugin, configures common
  compiler options as well as code quality checks.
* `myproject.library-conventions` - adds publishing configuration to publish to the organization's repository and checks for mandatory content in a README.
  It applies `java-library` and `maven-publish` plugins as well as the `myproject.java-conventions` plugin.

Note how applying a convention plugin to a subproject effectively declares its type.
By applying `myproject.java-conventions` plugin we state: this is a "Java" project.
By applying `myproject.library-conventions` plugin we state: this is a "Library" project.

In order for precompiled script plugins to be discovered, the `buildSrc` project needs to apply the `groovy-gradle-plugin` plugin
in its `build.gradle` file:

.Enabling precompiled script plugins
---
include::sample[dir="groovy",files="buildSrc/build.gradle[tags=apply]"]


== Things to note

=== Applying an external plugin in precompiled script plugin

The `myproject.java-conventions` plugin uses SpotBugs plugin to perform static code analysis.

SpotBugs is an external plugin - external plugins link:{userManualPath}/custom_plugins.html#applying_external_plugins_in_precompiled_script_plugins[need to be added as implementation dependencies] before they can be applied in a precompiled script plugin:
---
include::sample[dir="groovy",files="buildSrc/build.gradle[tags=repositories-and-dependencies]"]

* The dependency artifact coordinates (GAV) for a plugin can be different from the plugin id.
* The Gradle Plugin Portal (`gradlePluginPortal()`) is added as a repository for plugin dependencies.
* The plugin version is determined from the dependency version.

Once the dependency is added, the external plugin can be applied in precompiled script plugin by id:
---

include::sample[dir="groovy",files="buildSrc/src/main/groovy/myproject.java-conventions.gradle[tags=apply-external-plugin]"]


=== Applying other precompiled script plugins

Precompiled script plugins can apply other precompiled script plugins.

The `myproject.library-conventions` plugin applies the `myproject.java-conventions` plugin:
---
include::sample[dir="kotlin",files="buildSrc/src/main/kotlin/myproject.library-conventions.gradle.kts[tags=plugins]"]

=== Using classes from the main source set

Precompiled script plugins can use classes defined in the main source set of the plugins project.

In this sample, `myproject.library-conventions` plugin uses a custom task class from `buildSrc/src/main/java` to configure library README checks:
---
include::sample[dir="groovy",files="buildSrc/src/main/groovy/myproject.library-conventions.gradle[tags=use-java-class]"]


For more details on authoring custom Gradle plugins, consult the link:{userManualPath}/custom_plugins.html[user manual].

## Links
[Gradle piece of documentation](https://docs.gradle.org/current/samples/sample_convention_plugins.html)
[Share dependencies between gradle sub-projects](https://docs.gradle.org/current/userguide/declaring_dependencies_between_subprojects.html#declaring_dependencies_between_subprojects)
