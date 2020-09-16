# Initial page

A little motion planning library designed for FTC. Check out the source on [GitHub](https://github.com/acmerobotics/road-runner). 

## Quickstart

Check out the [quickstart](https://github.com/acmerobotics/road-runner-quickstart) for an example of Road Runner in practice.

## Installation

### Core \(FTC\)

1. Open your Android Studio project.
1. Open `TeamCode/build.release.gradle` and add `implementation 'com.acmerobotics.roadrunner:core:0.5.2'` to the end of the `dependencies` block.
1. Sync the project \(Android Studio should prompt you to do this\).

{% hint style="warning" %}
Adding Road Runner and its dependencies may put your install package over the method reference limit imposed by some versions of Android. This restriction can be circumvented by one of the following methods:

* **If you do not need to target API level 19** \(i.e., you don't need to use ZTE Speeds\), add `multiDexEnabled true` to the `defaultConfig` closure in `build.common.gradle`.
* Otherwise, the next best solution is to enable Proguard which culls unnecessary methods. Begin by downloading the `proguard-rules.pro` file from the [quickstart](https://github.com/acmerobotics/road-runner-quickstart/blob/master/TeamCode/proguard-rules.pro) and save it to the `TeamCode` directory. Then add following lines to the `debug` and `release` closures inside `buildTypes` in `build.common.gradle`:

```groovy
minifyEnabled true
proguardFiles getDefaultProguardFile('proguard-android.txt'),
        'proguard-rules.pro'
```

For more information, see [this article](https://developer.android.com/studio/build/multidex).
{% endhint %}

### GUI

Road Runner includes a simple GUI for generating trajectories from pose waypoints and constraints. You can download the latest version from the Releases tab \(or build it with `./gradlew shadowJar`\).

### Plugin

Road Runner also includes a simple IDEA/Android Studio plugin based upon the GUI. Here are some instructions for building and loading the plugin:

1. Download the latest plugin zip from the Release assets or build it with `./gradlew buildPlugin`.
1. In Android Studio, navigate to Settings &gt; Plugins.
1. Click the gear icon and select `Install Plugin from Disk...`.
1. Select the zip archive from your downloads or `plugin/build/distributions`.
1. Restart Android Studio to activate plugin changes.
1. Click on "Road Runner" on the right side of the editor and the tool window should appear.
