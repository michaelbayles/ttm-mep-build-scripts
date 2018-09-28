# TTM Mobile Ecosystem Build Scripts

There are many common elements required by all Mobile Ecosystem apps and components. This repository aims to collect those common elements into [Gradle script plugins](https://docs.gradle.org/current/userguide/plugins.html#sec:script_plugins) that can be referenced with as little modification to the module's build script as possible, minimizing human error and centralizing build behaviour.

## Referencing the Build Script

Referencing the build script is easy: simply add the Artifactory plugin and apply the script plugin directly from this repository via URL in your module's `build.gradle` file. If more than one module in your project is going to make use of this script plugin, the Artifactory plugin can be added to the root project's `build.gradle` file instead, but the script plugin must still be applied to each module individually.

First, add the `plugins` block with the Artifactory plugin to the top of the file (or below the `buildscript` block, if present):

```gradle
/* If there is a buildscript block, it should be here:
buildscript {
    ...
}
*/

plugins {
    id "com.jfrog.artifactory" version "4.7.5"
}

/* There are likely lines like this here:
apply plugin: 'com.android.application'

android {
    ...
}
*/
```

Finally, add the script plugin below the `android` block by referencing the latest version branch from this repository:

```gradle
/* There should be lines like this here:
android {
    ...
}
*/

apply from: 'https://www.github.com/geotracsystems/ttm-mep-build-scripts/raw/version/1/ttm_module.gradle'

/* There are likely lines like this here:
dependencies {
    ...
}
*/
```

### Additional Information

Adding the `plugins` block is required because the `plugins` block cannot be used in script plugins. [According to the Gradle documentation, this limitation will be lifted in future versions of Gradle.](https://docs.gradle.org/current/userguide/plugins.html#sec:build_scripts_only)

The script plugin needs to be applied below the `android` block so that changes made in the `android` block, such as the version name, can be used in the script plugin. This also allows the script plugin to override values, such as the build number, when necessary.

## Branching Strategy

This repository is specifically laid out to make it easy to add new features and fix bugs in build scripts without having to update every consumer of the build script. As such, there is a single number for version, and versions refer to a branch as opposed to a single tagged commit. For example, in referencing `version/1`, builds will automatically be updated to include new changes. These are guaranteed to not cause compatibility problems, so once the version is referenced, no further maintenance should be required of any consumer.

A new version branch would only ever be created if a significant change is being made which would have a major impact on consumers of the script plugin. In most cases, this is likely because the build scripts that include the script plugin would need to change themselves, but this would also apply if there would be a fundamental change to the artifacts being produced that would need consideration before updating an app's build script.

Once a new version exists, only bug fixes should ever be added to older versions. These bug fixes should be merged into newer versions if they are still applicable.

## Publishing

If your project will be publishing an AAR, you will also need to apply the [ttm_module_publish gradle file](ttm_module_publish.gradle):

```gradle
apply from: 'https://www.github.com/geotracsystems/ttm-mep-build-scripts/raw/master/ttm_module_publish.gradle'
```

Additionally, you will need to set a `version` property in the root project:

```gradle
version="1.0"
```

This `version` property will be used in the `versionName` as well as the metadata used in Artifactory. If the `buildNumber` property is also set, it will be appended onto `versionName` in the format `"{version}.{buildNumber}"`

`versionCode` will be set by the `buildNumber` property, which the CI system should inject. Otherwise, it will default to 1.

The branch name will determine the classifier and corresponding repository to publish to:


| Branch    | Classifier | Repository                          |
|  ---      |     ---    |     ---                             |
| master    |            | ttm-mobile-ecosystem-release-maven  |
| release/* | BETA       | ttm-mobile-ecosystem-snapshot-maven |
| hotfix/*  | BETA       | ttm-mobile-ecosystem-snapshot-maven |
| develop   | SNAPSHOT   | ttm-mobile-ecosystem-snapshot-maven |
