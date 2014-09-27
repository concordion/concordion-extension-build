concordion-extension-build
==========================

Contains a common build file for publishing Concordion extensions.

The common build file contains build logic to do a local Maven install, or publish to Maven Central as a snapshot or release.

It also allows you to publish output to Github Pages.

# Tasks
It adds the following tasks (amongst others) to your build:

 * `install` - builds the release and installs to your local Maven repository
 * `publishDocs` - builds the documentation and publishes to Github Pages
 * `publishRelease` - builds the release and publishes to the Sonatype OSS releases repository
 * `publishSnapshot` - builds the release and publishes to the Sonatype OSS snapshots repository

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

# Publishing a snapshot or release to Sonatype OSS (for Maven Central)
Only authorised administrators can publish to Sonatype OSS. Once authorised follow the instructions on [how to make a release](https://code.google.com/p/concordion/wiki/HowToMakeARelease).
