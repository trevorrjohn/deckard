# Deckard (for Gradle)

Deckard is the simplest possible Android project that uses Robolectric for testing and Gradle to build. It has one Activity, a single Robolectric test of that Activity.

With just a bit of manual intervention, Deckard also imports into IntelliJ, due to the support for gradle. See below for instructions.

## Setup

*Note: These instructions assume you have a Java 1.6 [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) installed.*

To start a new Android project:

1. Install the [Android SDK](http://developer.android.com/sdk/index.html). On Mac OS X with [Homebrew](http://brew.sh/) just run `brew install android-sdk`

2. Set your `ANDROID_HOME` environment variable to `/usr/local/opt/android-sdk`.

3. Run the Android SDK GUI and install API 18 and any other APIs you might need. You can start the GUI by invoking `android`

4. Download Deckard from GitHub:
    ```bash
    wget https://github.com/tjohn/deckard/archive/master.zip
    unzip master.zip
    mv deckard-master my-new-project
    ```

5. In the project directory you should be able to run the Robolectric tests:
    ```bash
    cd my-new-project
    ./gradlew clean test
    ```
6. Change the names of things from 'Deckard' to whatever is appropriate for your project. Package name, classes, and the AndroidManifest are good places to start.

7. Build an app. Win.

## IntelliJ / Android Studio Support

### Compatibility
Currently we believe only IntelliJ EAP build 135.666 or later will work with the tooling that deckard-gradle depends on.

### Importing
Import the project into IntelliJ 'Import Project' and selecting the project's `build.gradle`. When prompted, you can just pick the default gradle wrapper.

### REQUIRED: Tweaking the Module Dependency Order (i.e. Classpath)
For both IntelliJ IDEA and Android Studio, you will also need to change the classpath order for you dependencies. Otherwise you will see the dreaded `Stub!` exception:
```
    !!! JUnit version 3.8 or later expected:
    java.lang.RuntimeException: Stub!
      at junit.runner.BaseTestRunner.<init>(BaseTestRunner.java:5)
      at junit.textui.TestRunner.<init>(TestRunner.java:54)
      at junit.textui.TestRunner.<init>(TestRunner.java:48)
      at junit.textui.TestRunner.<init>(TestRunner.java:41)
```
* To fix this go to Project Preferences -> Gradle, in VM Options enter `-Dintellij=true`. This will run the `scripts/fix_intellij_dependencies.sh` script every time you refresh the gradle in intellij, thus eliminating the `Stub!` exception.

### IntelliJ Extra Step
Gradle is now in charge of compilation, but IntelliJ still launches the test runner. So in order for IntelliJ to know where to find compiled classes, you have to tell it. This manual step will hopefully go away soon, but for now it's necessary:

1. Go to Project Structure -> Modules -> deckard-gradle -> Paths.
2. The value for 'Output path' should be filled in, but 'Test output path' will not be. Copy the text that's in 'Output path', paste into 'Test output path', but change the final 'build/classes/debug' to 'build/test-classes'. This is because the gradle android test plugin currently dumps all compiled test output (for all variants) into the same directory. This means that currently variants are not fully supported.

### Running the Robolectric Test
You should now be able to `DeckardActivityRobolectricTest`. Run it as a normal JUnit test - make sure to choose the JUnit test runner and not the Android one.