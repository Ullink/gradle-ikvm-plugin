# Gradle IKVM Plugin [![Build status](https://ci.appveyor.com/api/projects/status/31ut9l170kfbjyq3?svg=true)](https://ci.appveyor.com/project/gluck/gradle-ikvm-plugin) [![Join the chat at https://gitter.im/Ullink/gradle-ikvm-plugin](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/Ullink/gradle-ikvm-plugin?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This plugin allows to compile the project jar into a .Net assembly using [IKVM](http://www.ikvm.net/).
It supports various IKVM flags, and also allows for .Net API documentation generation.

## Main Features
- Command line wrapper around IKVM.
- Supports all IKVM flags.
- Easy way to include 3rd party DLLs into the build process.
- No need to install IKVM locally as it can be downloaded during the build process. Handy for CI Enviroments.
- Works also on Unix & Mac by using [Mono](http://www.mono-project.com/).


Below tasks are provided by the plugin, if you apply the `ikvm` plugin. You can alternatively apply just the ikvm-base if you want to create your custom tasks.

## ikvm

This task depend on standard `jar` task, and builds the corresponding assembly.
It can be configured by a usual gradle convention.

Simplest usage:

Build script snippet can be used in all Gradle versions:

    buildscript {
        repositories {
            mavenCentral()
        }

        dependencies {
            classpath "com.ullink.gradle:gradle-ikvm-plugin:2.3"
        }
    }

    apply plugin:'java'
    apply plugin:'ikvm'

    repositories {
        mavenCentral()
    }

Build script snippet can be used instead with Gradle 2.1+

    plugins {
      id "com.ullink.ikvm" version "2.3"
    }

More options:

    // ikvmcompile allows to provide 3rd Party DLLs to the process (provided the dlls are available in some repository of yours)
    dependencies {
        ikvmcompile group: 'foo', name: 'bar', version: '1.0', type: '.dll'
    }

    ikvm {
        // If defined, the plugin will use IKVM_HOME environment variable ...
        // ... otherwise it will download (& cache) Ikvm release zip from official download links (sourceforge & www.frijters.net) ...
        ikvmVersion = '7.2.4630.4'

        // ... but you can also specify the Ikvm folder yourself then this will be used.
        ikvmHome = '../ikvm-0.46.0.1'

        // Below values have sensible __defaults__ but can be overriden

        // input jar(s) to transform to IL (defaults to jar task output)
        jars = [ jar.archivePath ]

        // Where will the assembly be generated
        destinationDir = jar.destinationDir

        // Assembly name
        assemblyName = project.name

        // Assembly version
        version = project.version

        // KeyFile for assembly signing
        keyFile = null

        // Generates PDB file for debugging purposes
        debug = true

        // Generates XML documentation file (from javadoc through custom DocLet)
        generateDoc = false

        // Whether to handle warnings as errors - not compatible with all IKVM versions
        // as it was introduced in IKVM 7. If necessary, use the code specific one
        warnAsError = true

        // Warning codes that should be considered as errors
        warnAsError = [105, 110, 120]

        // Other ikvmc options can be set:
        // fileVersion, target, main, classloader, delaySign, compressResources, removeAssertions, srcPath ...
        
        //removes the current CLR runtime directory from the search path, you should add reference dependencies manually see ikvmcompile
        nostdlib = true
    }

## Usage

When everything is setup correctly just run `gradle ikvm`.


## ikvmDoc

This task extends standard `javadoc` task, and allows XML documentation to be generated for the assembly.
It depends on `ikvm` task, and uses a custom DocLet to generate the documentation.
Note: you can also have the documentation generated by `ikvm` task, using `generateDoc = true`.

Sample usage: either invoke `gradle ikvmDoc` or customize it:

    task myDoc(type: IkvmDoc) {
        // customize javadoc options here
    }

# License

All these plugins are licensed under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html) with no warranty (expressed or implied) for any purpose.
