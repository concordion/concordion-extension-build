concordion-extension-build
==========================

Contains a common build file for publishing Concordion extensions. This includes common properties, dependencies and plugins, publishing artifacts to Bintray and documentation to Github Pages.

The common build file contains build logic to do a local Maven install, or publish a release to Bintray, JCenter and Maven Central.

It also allows you to publish output to Github Pages.

# Tasks
It adds the following tasks (amongst others) to your build, that you can run using Gradle

 * `./gradlew install` - builds the release and installs to your local Maven repository
 * `./gradlew publishDocs` - builds the documentation and publishes to Github Pages
 * `./gradlew publishRelease` - builds the release and publishes to Bintray and to the JCenter and Maven Central repositories
 * `./gradlew publishSnapshot` - builds the release and publishes to the Sonatype OSS snapshots repository

# Configuration
Example configuration is:

```gradle
apply from: 'https://raw.githubusercontent.com/concordion/concordion-extension-build/master/extension-build.gradle'

description = 'An extension to Concordion to use Excel spreadsheets rather than HTML as specifications'

ext {
    poiVersion = '3.10-FINAL'
    developers = {
        developer {
            id 'robmoffat'
            name 'Rob Moffat'
            roles { role 'Extension Contributor' }
            url 'http://kite9.com'
        }
    }
    inceptionYear = '2014'   
}

dependencies {
    compile "org.apache.poi:poi:$poiVersion"
    compile "org.apache.poi:poi-ooxml:$poiVersion"
    compile "org.apache.poi:poi-ooxml-schemas:$poiVersion"
    compile 'org.apache.commons:commons-lang3:3.3.2'
}

githubPages {
	repoUri = 'git@github.com:concordion/concordion-excel-extension.git'
	pages {
	   from 'docs'

		from ('build/reports/spec/spec/concordion/ext/excel') {
			into 'spec'
		}

		from ('build/docs/javadoc/') {
			into 'api'
		}
	}
}
```

The `dependencies` block is only required if you want to add dependencies over and above the concordion dependency.

The `githubPages` block defines the repository to publish to, and has a pages CopySpec that defines the pages to be copied.

# Publishing to Github Pages
Prior to publishing to Github Pages for the first time, you will need to [create a gh-pages branch](https://help.github.com/articles/creating-project-pages-manually#create-a-gh-pages-branch), and [add content and push](https://help.github.com/articles/creating-project-pages-manually#add-content-and-push).

# Publishing a snapshot or release
Only authorised committers can publish a release.

In order to publish to Bintray, you must have a Bintray account linked to the Concordion organisation, and must the following properties defined in in your `~/.gradle/gradle.properties` file (where ~ is whatever your home folder is).
 
 ```
 bintrayUsername=<your username>
 bintrayApiKey=<your api key>
 bintrayPassphrase=<concordion passphrase>
 sonatypeUsername=<concordion sonatype username>
 sonatypePassword=<concordion sonatype password>
 ``` 
 
 with your bintray username and api key. The api key is shown if you edit your Bintray profile. 
 
 The `bintrayPassphrase` is the passphrase for the GPG key that is set up for the Concordion organisation to sign the release. 
 
 The `sonatypeUsername` and `sonatypePassword` are the credentials to publish to Maven Central.

 The Concordion Project Lead will send the bintrayPassphrase, sonatypeUsername and sonatypePassword to contributors on request.