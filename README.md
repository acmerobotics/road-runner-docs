# Initial page

A little motion planning library designed for FTC. Check out the source on [GitHub](https://github.com/acmerobotics/road-runner). 

## Quickstart

Check out [the quickstart](https://github.com/acmerobotics/road-runner-quickstart) for an example of Road Runner in practice.

## Installation

### Core \(FTC\)

1. Open your `ftc_app` project in Android Studio.
2. Open up the Gradle file for the module you'd like to install in \(probably `TeamCode/build.release.gradle`\).
3. Add `implementation 'com.acmerobotics.roadrunner:core:0.4.3'` to the end of the `dependencies` block.
4. Sync the project \(Android Studio should prompt you to do this\).

{% hint style="warning" %}
Road Runner may exceed the method reference limit imposed by some versions of Android. Fortunately, there are a few ways around this. For more information, see [this article](https://developer.android.com/studio/build/multidex).

* **If you do not need to target API level 19** \(i.e., you don't need to use ZTE speeds\), add `multiDexEnabled true` to the `defaultConfig` closure \(likely in `build.common.gradle`\).
* Otherwise, the next best solution is to enable Proguard \(the pre Android 5.0 solution in the article is difficult to implement with the FTC SDK\). To accomplish this, download the `proguard-rules.pro` file from [the quickstart](https://github.com/acmerobotics/road-runner-quickstart/blob/master/TeamCode/proguard-rules.pro) and save it to your module folder. Finally, add following lines to the `debug` and `release` closures inside `buildTypes` \(this is also located in `build.common.gradle` for FTC\):

```groovy
minifyEnabled true
proguardFiles getDefaultProguardFile('proguard-android.txt'),
        'proguard-rules.pro'
```
{% endhint %}

### GUI

Road Runner includes a simple GUI for generating trajectories from pose waypoints and constraints. You can download the latest version from the Releases tab \(or build it with `./gradlew shadowJar`\).

### Plugin

Road Runner also includes a simple IDEA/Android Studio plugin based upon the GUI. Here are some instructions for building and loading the plugin:

1. Download the latest plugin zip from Releases or build it with `./gradlew buildPlugin`.
2. In Android Studio, navigate to Settings &gt; Plugins.
3. Click the button that reads `Install plugin from disk...`.
4. Select the zip archive from earlier.
5. Restart Android Studio to activate plugin changes.
6. Click on "Path Designer" on the right side of the editor and the tool window should appear.

