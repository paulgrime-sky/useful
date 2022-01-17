
This results in ARM java being loaded.

```
➜  id-cybertron git:(IPB-15148) ✗ java -version
openjdk version "11.0.11" 2021-04-20
OpenJDK Runtime Environment AdoptOpenJDK-11.0.11+9 (build 11.0.11+9)
OpenJDK 64-Bit Server VM AdoptOpenJDK-11.0.11+9 (build 11.0.11+9, mixed mode)
```


```
➜  id-cybertron git:(IPB-15148) ✗ sbt console
OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
...
Welcome to Scala 2.12.10 (OpenJDK 64-Bit Server VM, Java 11.0.11).
Type in expressions for evaluation. Or try :help.

scala> System.getProperty("java.home")
res0: String = /Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home

scala> System.getProperty("java.version")
res1: String = 11.0.11
```
