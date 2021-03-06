# build-on-change

Allows to incrementally build an application based on the Git history by comparing the current
branch to a reference branch (like master). Only projects having changed and projects depending 
on them will be built, all other projects skipped.
 
Recommended to efficiently build *pull requests* in larger multi-project setups. The plugin will only build 
what was changed or needs testing. It avoids unnecessary rebuilds of other parts.
 
The plugin can complement a Gradle remote build cache. In contrast to the build cache, unchanged projects
are not restored from cache, but skipped altogether. This gives better performance, in particular for 
IO-heavy tasks like building fat JARs or NPM-based sub-projects that incur high costs, even when restoring
from cache.


## Usage

Get the current version from [com.github.rmee.build-on-change](https://plugins.gradle.org/plugin/com.github.rmee.build-on-change).

The plugin can be applied with:

```
plugins {
    id 'com.jfrog.bintray' version '<VERSION>'
}
```

or

```
buildscript {
	dependencies {
	    ...
		classpath 'com.github.rmee:build-on-change:<VERSION>'
	}
}
```

```
apply plugin: 'build-on-change'
```

This will make a new task *buildDependentsOnChange* available
that behaves like *buildDependents*, but performing an additional
change check against the reference branch and skips the task if
no change is available.

```
gradlew buildDependentsOnChange
```

## Configuration

```
buildOnChange {
	referenceBranch = 'master'
	rebuildPaths += 'build.gradle'
}
```

The reference branch to compare against can be set with the `referenceBranch` property.
By default the master branch is chosen.

Changes to files like the root build.gradle can let the plugin trigger a full
rebuild. Any such file can be added to the `rebuildPaths` list. By default
all Gradle related files are set (`build.gradle`, `gradle.properties`, `settings.gradle`, 
Gradle wrapper).


## Git Implementation

[org.ajoberstar.grgit](https://plugins.gradle.org/plugin/org.ajoberstar.grgit) is 
used to access Git from Gradle. The plugin is automatically applied to the root project.