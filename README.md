What is this?
---------------
The android-apt plugin assists in working with annotation processors in combination with Android Studio. It has two purposes:

* Allow to configure a compile time only annotation processor as a dependency, not including the artifact in the final APK or library
* Set up the source paths so that code that is generated from the annotation processor is correctly picked up by Android Studio.

This plugin requires the `android` or `android-library` plugin (version 0.9.x or up) to be configured on your project.

Including and using the plugin in your build script
---------------------------------------------------
Add the following to your build script to use the plugin:
```
#!groovy
buildscript {
    repositories {
      mavenCentral()
    }
    dependencies {
        // replace with the current version of the Android plugin
        classpath 'com.android.tools.build:gradle:0.14.4'
        // the latest version of the android-apt plugin
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.4'
    }
}

apply plugin: 'com.android.application'
apply plugin: 'com.neenbedankt.android-apt'
```

Passing processor arguments
---------------------------
Some annotation processor may require to pass custom arguments, you can use `apt.arguments` for that purpose.
For instance AndroidAnnotations needs the following configuration:

```
#!groovy
apt {
    arguments {
            resourcePackageName "com.myapp.package.name"
            androidManifestFile variant.outputs[0].processResources.manifestFile
    }
}
```

The arguments are processed for each variant when the compiler is configured. From this closure you can reference `android`, `project` and `variant` for the current variant.

Configurating a compiler style dependency
-----------------------------------------
Annotation processors generally have a API and a processor that generates code that is used by the API. Depending on the project the processor and the API might be split up in separate dependencies. For example, [Dagger][1] uses two artifacts called _dagger-compiler_ and _dagger_. The compiler artifact is only used during compilation, while the main _dagger_ artifact is required at runtime.

To aid in configuring this dependency, the plugin adds a Gradle [configuration][2] named **apt** that can be used like this:

```
#!groovy
dependencies {
 apt 'com.squareup.dagger:dagger-compiler:1.1.0'
 compile 'com.squareup.dagger:dagger:1.1.0'
}
```

If your test code requires generated code to be visible in Android Studio, you can use the `androidTestApt` configuration:

```
#!groovy
dependencies {
 androidTestApt 'com.github.frankiesardo:android-auto-value-processor:0.1'
 androidTestCompile 'com.github.frankiesardo:android-auto-value:0.1'
}
```

Configuration of other annotation processors
--------------------------------------------
For annotation processors that include the API and processor in one artifact, there's no special setup. You just add the artifact to the _compile_ configuration like usual and everything will work like normal. Additionally, if code that is generated by the processor is to be referenced in your own code, Android Studio will now correctly reference that code and resolve references to it.

[1]:http://square.github.io/dagger
[2]:http://www.gradle.org/docs/current/userguide/artifact_dependencies_tutorial.html

History & Credits
---------------
This plugin is based on a [script][6] that I've been using for some time which is the result of [this post on Google+][7] and [this post on StackOverflow.com][8].
Variations of the SO post and my gists have been floating around for a while on the interwebs. That, and the fact that including scripts is a bit inconvenient pushed me to create this plugin.

License
-------
This plugin is created by Hugo Visser and released in the [public domain][3]. Feel free to use and adapt as you like.
To get in touch, hit me up on [Twitter][4] or [Google Plus][5].

[3]:http://unlicense.org/
[4]:https://twitter.com/botteaap
[5]:https://google.com/+hugovisser
[6]:https://bitbucket.org/qbusict/android-gradle-scripts/src/686ce2301245ab1f0e6a32fb20b4d246ef742223/annotations.groovy?at=default
[7]:https://plus.google.com/+HugoVisser/posts/VtGYV8RHwmo
[8]:http://stackoverflow.com/questions/16683944/androidannotations-nothing-generated-empty-activity