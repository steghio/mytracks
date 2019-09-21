# MyTracks

*A respawn of Google MyTracks* - Original project from [Clément Poulain](https://github.com/Plonk42/mytracks)

**MyTracks** was the best GPS tracking application. Lightweight, simple to use, well integrated with Google Drive, no extra crap. 
Until ~~the fire nation attacked~~ Google decided to kill it hoping you would switch to that pile of manure that is [Google fit](https://www.google.com/intl/en_us/fit/).

As valid alternatives I could only find [ViewRanger](https://play.google.com/store/apps/details?id=com.augmentra.viewranger.android) or [Open GPS Tracker](https://play.google.com/store/apps/details?id=nl.sogeti.android.gpstracker)
but they are just not as good in my opinion.

*MyTracks* was [open-sourced by Google](http://google-latlong.blogspot.fr/2010/05/code-for-my-tracks-is-now-yours.html) on May 28, 2010. It is licensed under the terms of [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0). 

On 2014, Google [announced](https://code.google.com/archive/p/mytracks/) updates will no longer be published as open-source, and source will be removed from Google Code on January 01, 2015.

On January 29, 2016, Google [announced](https://support.google.com/maps/answer/6333516) **MyTracks** will no longer be available after April 30, 2016. The application has been removed from Google Play.

The last open source version available is **2.0.6**, released in February 2014. This fork is based on this version.

-------

Releases since **2.0.5** :

| Version | Release date      | Min SDK | Changelog                                                         |
|---------|-------------------|---------|-------------------------------------------------------------------|
| 2.0.11  | January 28, 2016  |      14 |                                                                   |
| 2.0.10  | April 29, 2015    |      14 | A new map layer, Earth, for viewing tracks in 3D                  |
| 2.0.9   | December 11, 2014 |       9 | Sync to Google Fit                                                |
| 2.0.8   | October 16, 2014  |       9 | Support Android Wear. Remove sharing with other apps on the phone |
| 2.0.7   | June 18, 2014     |       9 | Export to Google Maps Engine. Remove export to Google Fusion Tables and Google Maps  |
| 2.0.6   | Jan 30, 2014      |       9 | Insert photo markers. Calculate calories burned Play multiple tracks in Google Earth |

----

More information about **Google MyTracks**:
- [Wikipedia page](https://en.wikipedia.org/wiki/MyTracks)
- [Google code archive](https://code.google.com/archive/p/mytracks/)
- [Release history on AppBrain](http://www.appbrain.com/app/my-tracks/com.google.android.maps.mytracks)

This version of the project adds explanatory steps to setup the development environment and get a successful build for Android versions up to 9. 
I do not have newer versions available so I cannot test there, and there is just no hope my computer can in this millenium run a full simulation of the app for debugging.

The following steps are the bare minimum set of requirements you need to follow in order to generate a working APK that lets you use ALL the functionality
from the app EXCEPT for the Google Drive sync. 

**So, what is it all about?**

The way Google killed the app, was to revoke the API keys it used. Meaning that by swapping those keys out with ones you own, everything works as expected.

Almost.

Something to be **EXTREMELY** aware of, is that by using **YOUR** API keys, **YOU** are **BILLABLE if you exceed the free tier!**

## Billing

Google offers a free tier, which varies per API. Currently for the [Maps](https://developers.google.com/maps/billing/gmp-billing) 
usage the application would do, it is free for enough requests to last YOU more than a month, which is a full billing cycle.

## How to build

### Getting the Android SDK

To get the [Android SDK](https://developer.android.com/studio/#downloads) you have two options:

- download [Android Studio](https://developer.android.com/studio/releases/), which is some [IntelliJ](https://www.jetbrains.com/idea/) version that includes everything you need already
- download the [SDK](https://developer.android.com/studio/releases/sdk-tools) only, which means you use the IDE you like

#### Downloading the SDK only and setting up your IDE

Of course, it is not as easy as clicking on a link and extracting a zip, but still easy enough 
(following commands are for Linux version, exact counterparts will work for other OSs):

1) from the [download page](https://developer.android.com/studio/#downloads) scroll to the *Command line tools only* section and download the appropriate version for you OS
2) extract the downloaded file where you like, then go into `sdk-tools-linux-VERSION/tools/bin/` folder
3) the source project uses:

    ````
    compileSdkVersion 17
    buildToolsVersion "23.0.2"
    ````

    so unless you desire to update the whole project, download those versions.
    
    Be aware, that by switching to a newer version you will also face [Apache HttpClient](http://hc.apache.org/httpclient-3.x/) issues: 
    [1](https://stackoverflow.com/questions/31433687/android-gradle-apache-httpclient-does-not-exist), 
    [2](https://stackoverflow.com/questions/26024908/apache-httpclient-android-gradle)

4) **unnecessary depending on your configuration** create an empty `repositories.cfg` file under `/home/USER/.android` folder
5) download the necessary components with [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager):

    ````
    ./sdkmanager --update
    ./sdkmanager --install "build-tools;23.0.2" "platforms;android-17"
    ````

6) link the Android SDK in your IDE for the project to the downloaded one, pointing to wherever you placed `sdk-tools-linux-VERSION`
7) **unnecessary depending on your configuration** add a `local.properties` file in the root folder of the project
with this content: `sdk.dir=/home/USER/sdk-tools-linux-VERSION`

### Getting the Google API keys

Two Google API keys are needed to build this project:
- [Google Maps SDK for Android API key](https://developers.google.com/maps/documentation/android-sdk/get-api-key)
- [Google Drive API key](https://developers.google.com/drive/api/v3/enable-drive-api)

Those keys can be obtained using the [Google API console](https://console.developers.google.com/apis/).

As keys are not meant to be shared, they are located in their own value file: `/myTracks/src/main/res/values/apikeys.xml`, which is *git-ignored*. This means you have to **create it in your local project** to be able to build. Here is how it should look:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="backup_api_key">xxxxxx</string>
  <string name="maps_api_key">yyyyyy</string>
</resources>
```

Once you have added this file, the project should be able to find and use your keys.

Note: you can use any other file if you prefer than `apikeys.xml`, but in that case be careful to not commit it.

**!!!! WARNING !!!!**

Due to changes in the [Google Drive API](https://developers.google.com/drive/android/deprecation), it does not matter if you have a valid API key for Google Drive
since the application code needs to be changed anyway. Currently it is not done, I will investigate which options are there.

Google provides a [sample app](https://github.com/gsuitedevs/android-samples/tree/master/drive/deprecation) to help with the migration, ideally that is all
that is needed.

Therefore, put anything as `backup_api_key` value, since you won't be able to sync with Google Drive anyway. Yet.

### Compiling and generating the APK

Now that everything is set, you can generate the APK with (from the project root folder):

````
./gradlew build
./gradlew assemble
````

If you have a very powerful computer like me, you will get many warnings that your memory is too low and bla bla, just sit through it and place a steak on your
machine, it should be ready by the time the build ends.

The build will generate multiple APK files under folder `mytracks/myTracks/build/outputs/apk`, the one you CAN install is called `myTracks-debug.apk`.

When trying to install it, your device will first prompt you to allow installation from untrusted sources - say yes, you can revert it back later - AND
warn you that *Google Play Protect* couldn't verify the package - choose to continue.

That's it, you finally have your awesome app back :)
