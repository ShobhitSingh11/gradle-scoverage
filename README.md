[![Build Status](https://travis-ci.org/scoverage/gradle-scoverage.png?branch=master)](https://travis-ci.org/scoverage/gradle-scoverage)

gradle-scoverage
================
A plugin to enable the use of Scoverage in a gradle Scala project.

Usage
-----

You can find instructions on how to apply the plugin at:  http://plugins.gradle.org/plugin/org.scoverage

### Available tasks

1. `reportScoverage`: Produces XML and HTML reports for analysing test code coverage.

2. `aggregateScoverage`: An experimental support for aggregating coverage statistics in composite builds.

    When applied on a project with sub-projects, the plugin will create the aggregation task `aggregateScoverage`, which
    will first generate reports for each project individually (including the parent project), and will then generate an
    aggregated result based on these reports.

    The aggregated report will override the parent-project specific report (`parent-project/build/reports/scoverage`).

    One can still use `reportScoverage` in order to generate a report without aggregation.

3. `checkScoverage`: Validates coverage according status according the generated reports (aggregated or not).

    `gradle checkScoverage` will automatically invoke `reportScoverage` but it won't generate aggregated reports.
    In order to check coverage of aggregated reports one should use `gradle checkScoverage aggregateScoverage`.
    
### Configuration

The plugin exposes multiple options that can be configured by setting them in an `scoverage` block within the project's
build script. These options are as follows:

You can configure the version of Scoverage that will be used. This plugin should 

* `scoverageVersion = <String>` (default `"1.3.1"`): The version of the scoverage scalac plugin. This (gradle) plugin
should be compatible with all 1+ versions.

* `scoverageScalaVersion = <String>` (default `"2.12"`): The scala version of the scoverage scalac plugin. This will
be overridden by the version of the `scala-library` compile dependency (if the dependency is configured).
  
* `coverageOutputCobertura = <boolean>` (default `true`): Enables/disables cobertura.xml file generation (for both aggregated and non-aggregated reports).

* `coverageOutputXML = <boolean>` (default `true`): Enables/disables scoverage XML output (for both aggregated and non-aggregated reports).

* `coverageOutputHTML = <boolean>` (default `true`): Enables/disables scoverage HTML output (for both aggregated and non-aggregated reports).

* `coverageDebug = <boolean>` (default `false`): Enables/disables scoverage debug output (for both aggregated and non-aggregated reports).

* `minimumRate = <double>` (default `0.75`): The minimum amount of coverage in decimal proportion (`1.0` == 100%)
required for the validation to pass (otherwise `checkScoverage` will fail the build). 

* `coverageType = <"Statement" | "Branch" | "Line">` (default `"Statement"`): The type of coverage validated by the
`checkScoverage` task. For more information on the different types, please refer to the documentation of the scalac
plugin (https://github.com/scoverage/scalac-scoverage-plugin).

### Run without normal compilation

By default, running any of the plugin tasks will compile the code both using "normal" compilation (`compileScala`)
and using the scoverage scalac plugin (`compileScoverageScala`).

In cases where you only wish to generate reports / validate coverage, but are not interested in publishing the code,
it is possible to only compile the code with the scoverage scalac plugin, thus reducing build times significantly.
In order to do so, simply add the arguments `-x compileScala` to the gradle execution.
For example: `gradle reportScoverage -x compileScala`.

Migration to 3.x
----------------

* No more `testScoverage` task; instead, `test` will run coverage whenever the build is invoked with any of the scoverage tasks.

* No more need to declare scalac dependencies:
```groovy
// can safely delete this from build scripts
dependencies {
    scoverage group: 'org.scoverage', name: 'scalac-scoverage-plugin_2.12', version: '1.3.1'
    scoverage group: 'org.scoverage', name: 'scalac-scoverage-runtime_2.12', version: '1.3.1'
}
```

* All configurations are configured in `scoverage` block. For instance:
```groovy
// do this
scoverage {
    minimumRate = 0.5
}

// instead of this
checkScoverage {
    minimumRate = 0.5
}
```

* No more need to declare aggregation task:
```groovy
// can safely delete this from build scripts
task aggregateScoverage(type: org.scoverage.ScoverageAggregate)
checkScoverage {
     reportDir = file("$buildDir/scoverage-aggregate")
}
```

Release history
---------------

##### (not released yet) - 3.0.0

* Auto resolution of scalac plugin dependencies.
* Aggregation task declared by default.
* Deletion of non-instrumented classes, allowing for better integration with other coverage tools such as cobertura.
* Ability to execute coverage without "normal" compilation, thus reducing build times.