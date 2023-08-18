Reproduce MJAVADOC-768

maven javadoc plugin has a hard time with JPMS module-info definitions.

two modules: thing1, thing2. Both depend on "jakarta.annotation" using `requires static`. This defines a *compile time* dependency.

For maven, compile time dependencies can be defined either as `<scope>provided</scope>` or using optional: `<optional>true</optional>`

thing1 uses optional.
thing2 uses provided.

doc is a maven modules that creates an aggregate documentation jar for thing1 and thing2. Except that it fails with

```
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-javadoc-plugin:3.5.0:aggregate-jar (javadoc-jar) on project doc: MavenReportException: Error while generating Javadoc:
[ERROR] Exit code: 1
[ERROR] /Users/henning/scratch/jdbug/thing2/src/main/java/module-info.java:4: error: module not found: jakarta.annotation
[ERROR]     requires static jakarta.annotation;
[ERROR]                            ^
[ERROR] /Users/henning/scratch/jdbug/thing1/src/main/java/module-info.java:4: error: module not found: jakarta.annotation
[ERROR]     requires static jakarta.annotation;
[ERROR]                            ^
[ERROR] 2 errors
[ERROR] Command line was: /Library/Java/JavaVirtualMachines/temurin-17.jdk/Contents/Home/bin/javadoc -J-Xmx1024m @options @packages @argfile
[ERROR]
[ERROR] Refer to the generated Javadoc files in '/Users/henning/scratch/jdbug/doc/target/apidocs' dir.
```

because neither add the optional and/or provided module to the module path. And the javadoc tool needs these dependencies present.
