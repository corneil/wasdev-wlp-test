# wasdev-wlp-test
Demonstrate bug in ant task

When invoking the task with installDir and outputDir even if outputDir contains the default value expected a server.config.dir and server.output.dir is calculated that is incorrect.

This project creates 2 sets of Gradle tasks for invoking the `net.wasdev.wlp.ant:server` task
In one case only serverName, operation and installDir is supplied and in the second case outputDir is added with same value as expected when not supplied.

Invoke using `./gradlew -i clean startServer`  
In the first case output is:
```
invoke:[operation:create, serverName:test, installDir:build\wlp]
[ant:antlib:net.wasdev.wlp.ant:server] CWWKM2001I: server.config.dir is build\wlp\usr\servers\test.
[ant:antlib:net.wasdev.wlp.ant:server] CWWKM2001I: server.output.dir is build\wlp\usr\servers\test.
[ant:antlib:net.wasdev.wlp.ant:server] CWWKM2001I: Invoke command is ["build\wlp\bin\server.bat", create, test].
[ant:antlib:net.wasdev.wlp.ant:server] Server test created.
```

Invoke using `./gradlew -i clean startServerBroken`  
In the second case:
```
invoke:[operation:create, serverName:test, installDir:build\wlp, outputDir:build\wlp\usr\servers\test]
[ant:antlib:net.wasdev.wlp.ant:server] CWWKM2001I: server.config.dir is build\wlp\usr\servers\test.
[ant:antlib:net.wasdev.wlp.ant:server] CWWKM2001I: server.output.dir is build\wlp\usr\servers\test\test.
[ant:antlib:net.wasdev.wlp.ant:server] CWWKM2001I: Invoke command is ["build\wlp\bin\server.bat", create, test].
[ant:antlib:net.wasdev.wlp.ant:server] CWWKE0005E: The runtime environment could not be launched.
[ant:antlib:net.wasdev.wlp.ant:server] CWWKE0045E: It was not possible to create the server called test because the server directory build\wlp\usr\servers\test already exists.
```

The folder doesn't exists before createServerBroken is executed because there is a check to skip the task if the server folder exists.


