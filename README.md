# mps-gradle-plugin

MPS (Meta Programming System) from Jetbrains is a powerful tool to create DSL languages. But it does not work with such package managers as maven and gradle, and every user knows how tiring it is to add jar files one by one to the MPS project, especially if you need to add a package like neo4j containing dozens of jar-files.

This plugin helps to solve the problem. All you need is to add the necessary dependencies to the selected Gradle configuration (with default name 'mps'), specify the name of the solution in the MPS project, and the plugin 
1. will copy all jars to the /lib folder inside this solution
2. will change solution .msd file to reflect new dependencies.
