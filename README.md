concordion-extension-build
==========================

Contains a common build file for publishing Concordion extensions. This includes common properties, dependencies and plugins, publishing artifacts to Maven Central and documentation to Github Pages.

# Tasks
It adds the following tasks (amongst others) to your build, that you can run using Gradle

 * `./gradlew publishToMavenLocal` - builds the release and installs to your local Maven repository
 * `./gradlew gitPublishPush` - builds the documentation and publishes to Github Pages
 * `./gradlew publishToSonatype` - builds the release and publishes to the Maven Central repositories

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
    implementation "org.apache.poi:poi:$poiVersion"
    implementation "org.apache.poi:poi-ooxml:$poiVersion"
    implementation "org.apache.poi:poi-ooxml-schemas:$poiVersion"
    implementation 'org.apache.commons:commons-lang3:3.3.2'
}

gitPublish {
	repoUri = 'git@github.com:concordion/concordion-excel-extension.git'
	contents {
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

The `gitPublish` block defines the repository to publish to, and has a pages CopySpec that defines the pages to be copied.

# Publishing to Github Pages
Prior to publishing to Github Pages for the first time, you will need to [create a gh-pages branch](https://help.github.com/articles/creating-project-pages-manually#create-a-gh-pages-branch), and [add content and push](https://help.github.com/articles/creating-project-pages-manually#add-content-and-push).

# Publishing a release
Only authorised committers can publish a release.

The project is released to Maven Central using a few plugins defined in `build.gradle`.

## <a name="Pre-conditions"></a>Pre-conditions[](#Pre-conditions)

*   A GPG client is installed on your command line path. For more information, please refer to [http://www.gnupg.org/](http://www.gnupg.org/).
*   You have created your GPG keys and distributed your public key. For more information, please refer to [Gradle Signing Plugin](https://docs.gradle.org/current/userguide/signing_plugin.html).
*   You have a [Sonatype JIRA account](https://issues.sonatype.org), which has [approval](https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide#SonatypeOSSMavenRepositoryUsageGuide-3.CreateaJIRAticket) for publishing to the Concordion project.

*   You have configured your gradle.properties, typically located in your ~/.gradle directory: 
```
sonatypeUsername=<your-jira-username>
sonatypePassword=<your-jira-password>

signing.keyId=<public GPG key>
signing.password=<private GPG key passphrase>
signing.secretKeyRingFile=<path to secret key ring file containing your private key>
```
where _<your-jira-username>_ and _<your-jira-password>_ are the credentials for your Sonatype JIRA account.

## Performing a release

* Check that `gradle.properties` contains the desired version number.
* Commit and push all the changes to GitHub. (The release plugin will fail if you have any changes that aren't committed and pushed.)
* Checkout the master branch, if not already checked out.
* Either run `./gradlew clean test javadoc gitPublishPush publishToSonatype closeSonatypeStagingRepository` and manually release the build from the Nexus staging repository or run `./gradlew clean test javadoc gitPublishPush publishToSonatype closeAndReleaseSonatypeStagingRepository` if you are confident. See the [Gradle Nexus Publish Plugin](https://github.com/gradle-nexus/publish-plugin) for more details.
<!--During this build, Gradle will prompt for the version number of the release, and for the next version number to use. See [here](https://github.com/townsfolk/gradle-release#introduction) for the steps taken by the release plugin.-->

## Announce

* Announce to the mailing list and Twitter
