> Most web applications are changed and adapted quite frequently and quickly. Their environment, for example the size and the behaviour of the user base, are constantly changing. What was sufficient yesterday can be insufficient today. Especially in a web environment it is important to monitor and continuously improve the internal quality not only when developing, but also when maintaining the software.
>
>[Jenkins](http://jenkins-ci-org) is the leading [open-source](http://en.wikipedia.org/wiki/Open_Source) [continuous integration](http://martinfowler.com/articles/continuousIntegration.html) server. Thanks to its thriving plugin ecosystem, it supports building and testing virtually any project.
>
> <cite>Sebastian Bergmann and contributors, <a href="http://jenkins-php.org/">Template for Jenkins Jobs for PHP Projects</a></cite>

The goals of the Jenkins job template for PHP projects also holds true for [Drupal](http://drupal.org) projects. However Drupal has it's own idiosyncrasies so <a href="#differences-from-php-template">tools and metrics differ</a>.

This project aims to provide a standard Jenkins job template for Drupal projects.

Along with the [Phing Drupal Template](http://reload.github.com/phing-drupal-template) and the [Drupal Jenkins demo](http://github.com/kasperg/drupal-jenkins-demo) it is a part of an effort to create better continuous integration tools for Drupal projects to improve quality.

## Required Jenkins Plugins
You need to install the following plugins for Jenkins:

* [Analysis Collector](https://wiki.jenkins-ci.org/display/JENKINS/Analysis+Collector+Plugin) (for processing various logfiles)
* [Checkstyle](http://wiki.jenkins-ci.org/display/JENKINS/Checkstyle+Plugin) (for processing [Drupal Coder module](http://drupal.org/project/coder) or [PHP_Codesniffer](http://pear.php.net/PHP_CodeSniffer) logfiles in Checkstyle format)
* [DRY](http://wiki.jenkins-ci.org/display/JENKINS/DRY+Plugin) (for processing [phpcpd](https://github.com/sebastianbergmann/phpcpd) logfiles in PMD-CPD format)
* [Phing](https://wiki.jenkins-ci.org/display/JENKINS/Phing+Plugin) (for running [Phing](http://phing.info) build files)
* [Plot](https://wiki.jenkins-ci.org/display/JENKINS/Plot+Plugin) (for processing [phploc](https://github.com/sebastianbergmann/phploc) CSV output)
* [PMD](http://wiki.jenkins-ci.org/display/JENKINS/PMD+Plugin) (for processing [PHPMD](http://phpmd.org/) logfiles in PMD format)
* [Static Code Analysis](https://wiki.jenkins-ci.org/display/JENKINS/Static+Code+Analysis+Plug-ins) (for processing various logfiles)

You can install these plugins using the web frontend at

    http://localhost:8080/pluginManager/available

or using the [Jenkins CLI](http://wiki.jenkins-ci.org/display/JENKINS/Jenkins+CLI):

    wget http://localhost:8080/jnlpJars/jenkins-cli.jar
    java -jar jenkins-cli.jar -s http://localhost:8080 install-plugin analysis-core
    java -jar jenkins-cli.jar -s http://localhost:8080 install-plugin analysis-collector
    java -jar jenkins-cli.jar -s http://localhost:8080 install-plugin checkstyle
    java -jar jenkins-cli.jar -s http://localhost:8080 install-plugin dry
    java -jar jenkins-cli.jar -s http://localhost:8080 install-plugin phing
    java -jar jenkins-cli.jar -s http://localhost:8080 install-plugin plot
    java -jar jenkins-cli.jar -s http://localhost:8080 install-plugin pmd
    java -jar jenkins-cli.jar -s http://localhost:8080 safe-restart

In the above, replace `localhost:8080` with the hostname and port of your Jenkins installation.

## Build Automation
The configuration of the job template follows the artifacts produced by a [Phing](http://www.phing.info/trac/) build.xml file based on the [Phing Drupal Template](http://reload.github.com/phing-drupal-template).

The template assumes that the build template has been added as a submodule in `\build` as described in the [Phing Drupal template documentation](http://reload.github.com/phing-drupal-template).

## Using the Job Template
1. Check out the `jenkins-drupal-template` project from Git:
    <pre><code>cd $JENKINS_HOME/jobs
    git clone git://github.com/reload/jenkins-drupal-template.git drupal-template
    chown -R jenkins:nogroup drupal-template/</code></pre>
2. Reload Jenkins' configuration, for instance using the Jenkins CLI:
    <pre><code>java -jar jenkins-cli.jar -s http://localhost:8080 reload-configuration</pre></code>
3. Click on *New Job*.
4. Enter a *Job name*.
5. Select *Copy existing job* and enter *drupal-template* into the *Copy from* field.
6. Click *OK*.
7. Disable the *Disable Build* option.
8. Fill in your *Source Code Management* information.
9. Configure a *Build Trigger*, for instance *Poll SCM*.
10. Click *Save*.

## Demo
The [drupal-jenkins-demo repository](http://github.com/kasperg/drupal-jenkins-demo) contains a Drupal project with the [Phing Drupal Template](http://reload.github.com/phing-drupal-template) and the [Token module](http://drupal.org/project/token) as example of custom code is continuously integrated for [demonstration](http://jenkins.kasper.reload.dk:8080/job/drupal-demo/) purposes.

## <span id="differences-from-php-template">Differences from PHP Template</span>
The job template has a number of differences from the original [Template for Jenkins Jobs for PHP Projects](http://jenkins-php.org). Some of these are due to Drupal tools and practices and others personal preference.

### Build file in separate project
Due to the extensive work required to setup an automated build for Drupal projects the build file has been split into [a separate project](http://reload.github.com/phing-drupal-template). This also makes the build file useful with other continuous integration systems.

### JDepend plugin removed
[The metrics calculated by PHP_Depend](http://pdepend.org/documentation/software-metrics.html) and displayed by the [JDepend plugin](https://wiki.jenkins-ci.org/display/JENKINS/JDepend+Plugin) are mostly related for object oriented programming. Most Drupal modules written using functional programming and thus the metrics are at best of little value, at worst misleading.

### xUnit plugin removed
Drupal tests are run through the [SimpleTest framework](http://drupal.org/simpletest). SimpleTest results can be written in JUnit XML format using [Drupal scripts](http://drupalcode.org/project/drupal.git/blob/refs/heads/7.x:/scripts/run-tests.sh#l145) or [Drush](http://drush.ws/help/5#test-run) and Jenkins understands this format natively so there is no need for the plugin.

### Static Code Analysis plugins replaces Violations
The [Static Code Analysis plugins suite](https://wiki.jenkins-ci.org/display/JENKINS/Static+Code+Analysis+Plug-ins) and [Violations plugin](https://wiki.jenkins-ci.org/display/JENKINS/Violations) both support parsing and rendering static code analysis results. The Static Code Analysis plugins are used as they are have more features and are frequently updated.

## Support
[Issues are tracked on GitHub](https://github.com/reload/jenkins-drupal-template/issues). Comments and pull requests welcome.
