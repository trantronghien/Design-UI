## Overview

[Travis](https://travis-ci.com/) is a continuous integration service that enables you to run tests against your latest Android builds.  You can setup your projects to run both unit and integration tests, which can also include launching an emulator.  

### Signing Up

Login to [Travis CI](https://travis-ci.com/) and sign-in with your GitHub credentials.  You will need to grant access to any repositories that will be used.  If you are using a private repository and don't intend for the source code to be shared, you will need to signup for a paid subscription plan.

### Setup

You simply need to create a `.travis.yml` file in the root directory.  The simplest configuration to install the Build Tools and Android SDK 24.   You can launch the [[Gradle wrapper|Getting Started with Gradle]] to build and run emulator tests.    Make sure the `tools` line is first to ensure that Build Tools (esp for versions above API 24).  It also needs to be included twice.

```yaml
language: android
android:
  components:
    - tools # to get the new `repository-11.xml`
    - tools # see https://github.com/travis-ci/travis-ci/issues/6040#issuecomment-219367943)
    - platform-tools
    - build-tools-24.0.2
    - android-24

script:
   - ./gradlew build connectedCheck
```

See the [docs](http://docs.travis-ci.com/user/languages/android/) here for more information.  By default, all SDK license agreements are accepted but you can also dictate which ones to accept.

#### Design Support Library

If you are intending to use the new [[Design Support Library]], you will need to make sure to include the `extra-android-m2repository` component in `travis.yml`:

```yaml
language: android
android:
  components:

     - extra-android-m2repository
```

Otherwise, you are likely to see the following error message:

```
Searched in the following locations:
  file:/usr/local/android-sdk/extras/google/m2repository/com/android/support/support-v4/22.2.0/support-v4-22.2.0.pom 
  file:/usr/local/android-sdk/extras/google/m2repository/com/android/support/support-v4/22.2.0/support-v4-22.2.0.jar
```

The reason is that the [[Design Support Library]] must be downloaded from the [[SDK Manager|https://developer.android.com/tools/help/sdk-manager.html]], which looks in the [repository manifest](https://dl-ssl.google.com/android/repository/addon.xml) for the `Local Maven repository for Support Libraries` package.  The support library is not yet available on a public Maven repository for licensing reasons.

#### Google Play Services

If you intend to use Google Play Services with Travis, you will also want to add the `extra-google-m2repository` for similar reasons:

```yaml
language: android
android:
  components:
     - extra-google-m2repository
```

### Lambda expressions support

To add support for [[lambda expressions|Lambda Expressions]], make sure to specify JDK 8 or higher:

```yaml
jdk:
  - oraclejdk8
```

### Troubleshooting

If you see an error code 137, chances are that the Travis build has ran out of memory trying to load all your dependencies.

```bash
com.android.ide.common.process.ProcessException: org.gradle.process.internal.ExecException: Process 'command '/usr/lib/jvm/java-7-oracle/bin/java'' finished with non-zero exit value 137
```

Here are a few ways to try to resolve:

- If you are using [Google Play Services](https://developers.google.com/android/guides/setup), try to be more selective about the modules you import.  In prior versions before 6.5, you had to include all packages which often caused the 65K method limit to be reached.  If you only wish to use the Google Cloud Messaging package and not Google Fitness or Google Wear, you do not need to import 
`com.google.android.gms:play-services:8.3.0`.  Instead, you can simply specify the libraries explicitly:

  ```gradle
  compile 'com.google.android.gms:play-services-gcm:8.3.0'
  ```

- If you are still having issues, you will likely need to disable predexing, which compiles each dependency individually into Android bytecode and turns each module into a `.dex` file.  The problem is that this process is more memory intensive and takes longer and provides benefits only when doing incremental builds.  See  [this section](http://tools.android.com/tech-docs/new-build-system/tips#TOC-Improving-Build-Server-performance.) for more information.

  To disable pre-dexing, add this line to your root `build.gradle` to detect whether Travis is currently running:

  ```java
  ext {
      travisBuild = System.getenv("TRAVIS") == "true"
      // allows for -Dpre-dex=false to be set
      preDexEnabled = "true".equals(System.getProperty("pre-dex", "true"))
  }
  ```

  Then add this line to your `app/build.gradle`:

   ```gradle
   android {
     dexOptions {
        // Skip pre-dexing when running on Travis CI or when disabled via -Dpre-dex=false.
        preDexLibraries = preDexEnabled && !travisBuild
      }
    }
   ```


- Disable container-based builds, which currently only have a maximum of 4GB of memory, whereas standard versions have a max of 7.5GB of memory (according to this [doc](https://docs.travis-ci.com/user/ci-environment/)):

  ```yaml
  sudo: required
  ```

- If you want to print any lint error issues, add this line:

  ```yaml
  after_failure: "cat $TRAVIS_BUILD_DIR/app/build/outputs/lint-results-debug.xml"
  ```
