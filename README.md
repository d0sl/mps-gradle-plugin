# mps-gradle-plugin

MPS (Meta Programming System) from Jetbrains is a powerful tool to create DSL languages. But it does not work with `maven` and `gradle`, and everyone knows how tiring it is to add jar files one by one to the MPS project, especially if you need to add a package like neo4j containing dozens of jar-files.

This plugin helps to solve the problem. All you need is to add the necessary dependencies to the selected Gradle configuration (with default name `mps`), specify the name of the solution in the MPS project and the plugin 
1. will copy all jars to the /lib folder inside this solution
2. will change solution's .msd file to reflect new dependencies

## Let's start

1. First you have to create in your MPS project the solution with name `external`, which will accumulate the necessary libraries.
2. In `Module properties` of the created solution, find the dependency tab and add two dependencies: `JDK` and `external` (the solution itself). To the left of the dependency `external`, switch on the checkbox. This is necessary so that other modules in your project can add the `external` to their dependencies and thus connect all the added libraries.

You are now ready to use Gradlе to add dependencies to the MPS project. Place the following simple build.gradle in the root of your MPS project:

```
apply plugin: 'java'
apply from: 'https://raw.githubusercontent.com/d0sl/mps-gradle-plugin/master/mps.gradle'

repositories {
    mavenCentral()
}

dependencies {
    mps group: 'org.neo4j', name: 'neo4j', version: '3.5.3'
}
```

`apply from: 'https://raw.githubusercontent.com/d0sl/mps-gradle-plugin/master/mps.gradle'` applies the mps-gradle-plugin from the mps.gradle file directly from the github repository. You can download mps.gradle and use the local path to it.

`dependencies {
    mps group: 'org.neo4j', name: 'neo4j', version: '3.5.3'
}` Adds a dependency (in our example neo4j) to the `mps` configuration that we want to add to our MPS project.

Run `gradle mps_compose` and you will get something like this:

```
... SKIPPED ...
Performing bcprov-jdk15on.jar (org.bouncycastle:bcprov-jdk15on:1.60)
  -> adding modelRoot ${module}/lib/bcprov-jdk15on-1.60.jar!
  -> adding stubModelEntry ${module}/lib/bcprov-jdk15on-1.60.jar
Performing jProcesses.jar (org.jprocesses:jProcesses:1.6.4)
  -> adding modelRoot ${module}/lib/jProcesses-1.6.4.jar!
  -> adding stubModelEntry ${module}/lib/jProcesses-1.6.4.jar
Performing commons-compress.jar (org.apache.commons:commons-compress:1.18)
  -> adding modelRoot ${module}/lib/commons-compress-1.18.jar!
  -> adding stubModelEntry ${module}/lib/commons-compress-1.18.jar
Performing WMI4Java.jar (com.profesorfalken:WMI4Java:1.6.1)
  -> adding modelRoot ${module}/lib/WMI4Java-1.6.1.jar!
  -> adding stubModelEntry ${module}/lib/WMI4Java-1.6.1.jar
Performing jPowerShell.jar (com.profesorfalken:jPowerShell:1.9)
  -> adding modelRoot ${module}/lib/jPowerShell-1.9.jar!
  -> adding stubModelEntry ${module}/lib/jPowerShell-1.9.jar

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
```

Now, if you open your MPS project, you will see Java libraries added to the solution `extrenal`. And in the `Logical View` tree you should see the `stubs` subtree appeared.

If you go to the root directory of your project you can see all the jar files added inside `solutions/external/lib` directory.

> Notice that each command launch `mps_compose` clears all previous dependencies and creates them again. That is, you can change the configuration `mps` of dependencies in the build.gradle file and all changes after the command launch `mps_compose` are reflected in the MPS project. In particular, the following configuration will remove all dependencies from the solution:

```
dependencies {
//    mps group: 'org.neo4j', name: 'neo4j', version: '3.5.3'
}
```