# Part 1 – APK Decompilation

## What is an APK

An APK (Android Package Kit) is the file format used to distribute and install applications on Android devices. It contains all components required for an application to run, including compiled code (DEX files), resources, assets, and the AndroidManifest configuration file.

## Why Decompilation is Needed for Auditing

The source code of Android applications is usually not publicly available. Therefore, APK files need to be decompiled during security analysis in order to understand the internal logic of the application. 

For example, in this analysis, decompiling the APK allows us to inspect the login logic of the application, such as the `checkCredentials()` method. By examining this code, we can understand how the application validates user credentials and determine whether potential security issues exist.