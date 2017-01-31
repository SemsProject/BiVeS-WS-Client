Build BiVeS Web Service Client 
================================

When you've cloned the source code:

```sh
git clone git://sems.uni-rostock.de/bivesws-client
```

There are two supported options to build this project:

* [Build with Maven](#build-with-maven)
* [Build with Ant](#build-with-ant)


Build with Maven 
-----------------

[Maven](https://maven.apache.org/) is a build automation tool. We ship a `pom.xml` together with the sources which tells maven about versions and dependencies. Thus, maven is able to resolve everything on its own and, in order to create the library, all you need to call is `mvn package`:

```sh
usr@srv $ mvn package

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running de.unirostock.sems.bives.webservice.client.BivesWsTest
Tests run: 4, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.428 sec

Results :

Tests run: 4, Failures: 0, Errors: 0, Skipped: 0
```

That done, you'll find the binaries in the `target` directory.

Build with Ant 
---------------

[Ant](https://ant.apache.org/) is an Apache tool for automating software build processes. There is a `build.xml` file included in the source code that tells ant what to do. Since ant is not able to resolve the dependencies you need to create a directory `lib` containing the following libraries:

* [HttpClient](https://hc.apache.org/downloads.cgi)
* [Gson](https://code.google.com/p/google-gson/downloads/list)

We defined multiple targets in the `build.xml`. They can be displayed by calling `ant -p`:

```sh
usr@srv $ ant -p
Buildfile: /path/to/bivesws-client/build.xml

        BiVeS - BioModel Version Control System
        This package provides a Java Client for the BiVeS web service
    
Main targets:

 clean    clean up
 compile  compile the source
 dist     generate the distribution
 init     initialize workspace
 sign     sign a dist
Default target: dist
```

* `clean up` will delete all compiled files and produced libraries
* `compile` compiles the source code
* `dist` bundles all compiled binaries into a `jar` package

For example, to create the `jar` library just run `ant dist`:

```sh
usr@srv $ ant dist
Buildfile: /path/to/bivesws-client/build.xml

init:
    [mkdir] Created dir: /path/to/bivesws-client/build
    [mkdir] Created dir: /path/to/bivesws-client/dist

compile:
    [javac] Compiling 13 source files to /path/to/bivesws-client/build

dist:
      [jar] Building jar: /path/to/bivesws-client/dist/BiVeS-WS-Client-1.1.jar
      [jar] Building jar: /path/to/bivesws-client/dist/BiVeS-WS-Client-1.1-fat.jar

BUILD SUCCESSFUL
Total time: 7 seconds
```

