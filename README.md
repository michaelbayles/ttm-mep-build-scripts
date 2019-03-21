# TTM Mobile Ecosystem Build Scripts

The build scripts are now housed inside Bitbucket and Artifactory.
See https://bitbucket.trimble.tools/projects/MAINE/repos/infrastructure-gradle-plugins for more information.

This repo does contain one small Gradle script to reference and pull the custom Gradle plugins.
Add it to your project's `build.gradle` file as the last plugin applied:

```gradle
/* There are likely lines like this here:
plugins {
    id 'org.gradle.sample.goodbye'
}
apply plugin: 'com.android.application'
...
...
*/
apply from: 'https://raw.githubusercontent.com/geotracsystems/ttm-mep-build-scripts/version/3/ttm_module.gradle'

/* There are likely lines like this here:
android {
    ...
}
*/
```


Make sure to have properties `artifactory_user` and `artifactory_password` in your gradle.properties file.