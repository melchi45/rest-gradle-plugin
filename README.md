= REST Gradle Plugin for gradle 7 =
After gradle version is updated to 7, RestTask of 10ne throws an error. If an error occurs in @Internal annotation in Gradle 7, please replace RestTask with the corresponding module. Since the developer of 10ne does not have any more upate, the distribution package suitable for gradle 7 has been distributed as a package on github.

```shell
apply plugin: "io.github.melchi45.rest"
import io.github.melchi45.gradle.rest.*
buildscript {
    repositories {
        mavenCentral()
        maven {
            name = "GitHubPackages"
            url = uri("https://maven.pkg.github.com/melchi45/rest-gradle-plugin")
            credentials {
                username = project.findProperty("gpr.user") ?: System.getenv("USERNAME")
                password = project.findProperty("gpr.key") ?: System.getenv("TOKEN")
            }
        }
        dependencies {
            classpath "io.github.melchi45.gradle:rest-gradle-plugin:0.5.3"
        }
    }
}

afterEvaluate {
    preBuild.dependsOn restTask
}

task restTask(type: io.github.melchi45.gradle.rest.RestTask){
    // TODO: implement your rest task in here follow below description.
}
```

== A Gradle plugin that provides a task infrastructure to perform REST requests ==

=== Installation ===

[![Gradle](https://github.com/melchi45/rest-gradle-plugin/actions/workflows/gradle-publish.yml/badge.svg)](https://github.com/melchi45/rest-gradle-plugin/actions/workflows/gradle-publish.yml)

See the http://plugins.gradle.org/plugin/org.tenne.rest[Gradle Plugin Portal] for installation instructions.

=== Usage ===

The plugin adds a new task named `rest`. This task exposes the following properties::
* httpMethod - The type of HTTP method to execute. Type: String. Default: `get`. Possible values: `delete`, `get`, `head`, `options`, `post`, `put`.
* uri - The target URI of the request. An invocation of toString() on the value should result in a valid URI. Type: Object.
* username - Authentication username. Type: String.
* password - Authentication password. Type: String.
* contentType - The expected content type of both request and response. Type: groovyx.net.http.ContentType / String.
* requestContentType - The expected content type of the request. Overrides the `contentType` parameter. Type: groovyx.net.http.ContentType / String.
* requestBody - The request content. Type: Object.
* requestHeaders - Additional request headers. Type: Map.
* responseHandler - A custom successful request handler. Type: groovy.lang.Closure. The closure accepts one parameter. May accept the types String, InputStream or Object which falls back to the client's default handler.

For example, a POST request task:
[source,groovy]
```shell
task attack(type: io.github.melchi45.gradle.rest.RestTask) {
    httpMethod = 'post'
    uri = 'https://battle.server.com/attack'
    username = 'player'
    password = 'password'
    requestBody = [battleCry: 'FOR LEEROY JENKINS!']
    contentType = groovyx.net.http.ContentType.JSON
    requestHeaders = [customHeader: 'WoW']
    responseHandler = {
        //Configured content type is JSON and we let the client's handler parse the response for us
        assert it.message == 'success'
    }
}
```
