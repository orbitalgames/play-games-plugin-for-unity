# Google Play Games plugin for Unity
_Copyright (c) 2014 Google Inc. All rights reserved._

The Google Play Games plugin for Unity&reg; is an open-source project whose goal
is to provide a plugin that allows game developers to integrate with
the Google Play Games API from a game written in Unity&reg;. However, this project is
not in any way endorsed or supervised by Unity Technologies.

_Unity&reg; is a trademark of Unity Technologies._

## Overview

The Google Play Games plugin for Unity allows you to access the Google Play Games
API through Unity's [social interface](http://docs.unity3d.com/Documentation/ScriptReference/Social.html).
The plugin provides support for the
following features of the Google Play Games API:<br/>

* sign in
* unlock/reveal/increment achievement
* post score to leaderboard
* cloud save read/write
* show built-in achievement/leaderboards UI
* [turn-based multiplayer](TBMP.md)
* [real-time multiplayer](RTMP.md)

Features:

* easy GUI-oriented project setup (integrated into the Unity GUI)
* cross-platform support with no need for platform glue code.
* (Android) no need to override/customize the player Activity
* (Android) no need to override/customize AndroidManifest.xml

System requirements:

* Unity&reg; 4.5 or above
* To deploy on Android:
    * Android SDK
    * Google Play Services library, version 6.1.11 or above


## Upgrading

If you have already integrated your project with a previous version of the plugin and wish to upgrade to a new version, please refer to the [upgrade instructions](UPGRADING.txt).

## Configure Your Game

To use the plugin, you must first [configure your
game](https://developers.google.com/games/services/console/enabling) in the
Google Play Developer Console. Follow the instructions on creating a client ID
for Android. Be particularly careful when entering your package name and your
certificate fingerprints, since mistakes on those screens can be difficult to
recover from.

If you intend to use real-time or turn-based multiplayer in your game, remember
to activate those features in the Google Play Developer Console when creating
your application instances.

Please note the following pieces of information when creating your client IDs,
as they will be necessary later:

* Your package name (e.g. "com.example.awesomegame")
* Your application ID (e.g. 123456789012)


**Note:** Do not forget to add your test accounts (the accounts with which you
will try signing in) to the **Testing** section of the Developer Console,
otherwise you will not be able to sign in to your game.

## Add Achievements and Leaderboards

Add
[achievements](https://developers.google.com/games/services/common/concepts/achievements)
and
[leaderboards](https://developers.google.com/games/services/common/concepts/leaderboards)
to your game in the Google Play Developer Console. For each achievement and
leaderboard you configure, make sure to note the corresponding **achievement ID** or **leaderboard ID**, as those will be needed when making the API calls.
Achievement and leaderboard IDs are alphanumeric strings (e.g.
"Cgkx9eiuwi8_AQ").

## Load Your Game Project

Next, load your game project into the Unity editor.

If you do not have a game project to work with, you can use the **Minimal** sample
available in the **samples** directory. Using that sample will allow you to
quickly test your setup and make sure you can access the API.

If you want to test a larger sample after you are familiar with the plugin,
try the **CubicPilot** game.
More information about building the samples can be found in the
[samples README](samples/README.md) file.

## Plugin Installation

To download the plugin, clone this Git repository into your file system (or download it as
a ZIP file and unpack it). Then, look for the **unitypackage** file in
the **current-build** directory:

    current-build/GooglePlayGamesPluginForUnity-X.YY.ZZ.unitypackage

To install the plugin, simply open your game project in Unity and import that file into
your project's assets, as you would any other Unity package. This is accomplished through
the **Assets | Import Package | Custom Package** menu item (you can also reach this menu it
by right-clicking the **Assets** folder). After importing, you should see a new menu item
was added to the File menu: **"Play Games Android setup"**. If you don't see the new menu
item, refresh the assets by clicking **Assets | Refresh** and try again.

## Android Setup

To configure your Unity game to run with Google Play Games on Android, first
open the Android SDK manager and verify that you have downloaded the **Google
Play Services** package. The Android SDK manager is usually available in your
SDK installation directory, under the "tools" subdirectory, and is called
**android** (or **android.exe** on Windows). The **Google Play Services**
package is available under the **Extras** folder. If it is not installed
or is out of date, install or update it before proceeding.

Next, set up the path to your Android SDK installation in Unity. This is located in the
preferences menu, under the **External Tools** section.

Next, configure your game's package name. To do this, click **File | Build Settings**,
select the **Android** platform and click **Player Settings** to show Unity's
Player Settings window. In that window, look for the **Bundle Identifier** setting
under **Other Settings**. Enter your package name there (for example
_com.example.my.awesome.game_).

Next, click the **File | Play Games - Android setup** menu item. This will display the Android setup screen, where you must input your Application ID (e.g. 12345678012).

After filling in the application ID, click the **Setup** button.

**Important:** The application ID and package name settings must match exactly
the values you used when setting up your project on the Developer Console.

### Additional instructions on building for Android on Windows

If you are using Windows, you must make sure that your Java SDK installation can be accessed by Unity. To do this:

1. Set the JAVA_HOME environment variable to your Java SDK installation path (for example, `C:\Program Files\Java\jdk1.7.0_45`).
2. Add the Java SDK's `bin` folder to your `PATH` environment variable (for example, `C:\Program Files\Java\jdk1.7.0_45\bin`)
3. Reboot.

**How to edit environment variables:** In Windows 2000/XP/Vista/7,
right-click **My Computer**, then **Properties**, then go to **Advanced System Properties**
(or **System Properties** and then click the **Advanced** tab), then
click **Environment Variables**. On Windows 8, press **Windows Key + W** and
search for **environment variables**
For more information, consult the documentation for your version of Windows.


## Run the Project

If you are working with the **Minimal** sample, you should be able to build
and run the project at this point. You will see a screen with an **Authenticate** button,
and you should be able to sign in when you click it.

To build and run on
Android, click **File | Build Settings**, select the **Android** platform, then
**Switch to Platform**, then **Build and Run**.

The remainder of this guide assumes you are now attempting to write your
own code to integrate Play Games services into your game.

## ISocialPlatform Compliance

The Google Play Games plugin implements a subset of Unity's [social interface](http://docs.unity3d.com/Documentation/ScriptReference/Social.html), for compatibility
with games that already use that interface when integrating with other
platforms. However, some features are unique to Play Games and are
offered as extensions to the standard social interface provided by Unity.

The standard API calls can be accessed through the **Social.Active** object,
which is a reference to an **ISocialPlatform** interface. The non-standard
Google Play Games extensions can be accessed by casting the **Social.Active**
object to the **PlayGamesPlatform** class, where the additional methods are
available.


## Initialization

To initialize the plugin and make it your default social platform,
call **PlayGamesPlatform.Activate**:

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    ...

    // recommended for debugging:
    PlayGamesPlatform.DebugLogEnabled = true;

    // Activate the Google Play Games platform
    PlayGamesPlatform.Activate();
```

After activated, you can access the Play Games platform through
**Social.Active**. You should only call **PlayGamesPlatform.Activate** once in
your application. Making this call will not display anything on the screen and
will not interact with the user in any way.

## Sign in

To sign in, call **Social.localUser.Authenticate**, which is part of the
standard Unity social platform interface.

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    ...
    // authenticate user:
    Social.localUser.Authenticate((bool success) => {
        // handle success or failure
    });
```

Authentication will show the required consent dialogs. If the user has already
signed into the game in the past, this process will be silent and the user will
not have to interact with any dialogs.

Note that you cannot make any games API calls (unlock achievements, post scores,
etc) until you get a successful return value from **Authenticate**, so it is
good practice to put up a standby screen until the callback is called, to make
sure the user can't start playing the game until the authentication process
completes.

## Revealing/Unlocking an Achievement

To unlock an achievement, use the **Social.ReportProgress** method with a
progress value of 100.0f:

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    ...
    // unlock achievement (achievement ID "Cfjewijawiu_QA")
    Social.ReportProgress("Cfjewijawiu_QA", 100.0f, (bool success) => {
        // handle success or failure
    });
```

Notice that according to the expected behavior of
[Social.ReportProgress](http://docs.unity3d.com/Documentation/ScriptReference/Social.ReportProgress.html),
a progress of 0.0f means revealing the achievement and a progress of 100.0f
means unlocking the achievement. Therefore, to reveal an achievement (that was
previously hidden) without unlocking it, simply call Social.ReportProgress with
a progress of 0.0f.

## Incrementing an Achievement

If your achievement is incremental, the Play Games implementation of
**Social.ReportProgress** will try to behave as closely as possible to the
expected behavior according to Unity's social API, but may not be exact. For
this reason, we recommend that you do not use Social.ReportProgress for
incremental achievements. Instead, use the
**PlayGamesPlatform.IncrementAchievement** method, which is a Play Games
extension.

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    ...
    // increment achievement (achievement ID "Cfjewijawiu_QA") by 5 steps
    ((PlayGamesPlatform) Social.Active).IncrementAchievement(
        "Cfjewijawiu_QA", 5, (bool success) => {
            // handle success or failure
    });
```

## Posting a Score to a Leaderboard

To post a score to a leaderboard, call **Social.ReportScore**.

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    ...
    // post score 12345 to leaderboard ID "Cfji293fjsie_QA")
    Social.ReportScore(12345, "Cfji293fjsie_QA", (bool success) => {
        // handle success or failure
    });
```

Note that the platform and the server will automatically discard scores that are
lower than the player's existing high score, so you can submit scores freely
without any checks to test whether or not the score is greater than the player's
existing score.

## Showing the Achievements UI

To show the built-in UI for all leaderboards, call
**Social.ShowAchievementsUI**.

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    ...
    // show achievements UI
    Social.ShowAchievementsUI();
```

This will show a standard UI appropriate for the look and feel of the platform.

## Showing the Leaderboard UI

To show the built-in UI for all leaderboards, call **Social.ShowLeaderboardUI**.

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    ...
    // show leaderboard UI
    Social.ShowLeaderboardUI();
```

If you wish to show a particular leaderboard instead of all leaderboards, you
can pass a leaderboard ID to the method. This, however, is a Play Games
extension, so the Social.Active object needs to be cast to a PlayGamesPlatform
object first:

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    ...
    // show leaderboard UI
    ((PlayGamesPlatform) Social.Active).ShowLeaderboardUI("Cfji293fjsie_QA");
```

## Saving Game State to the Cloud (only on Android)

To save game state to the cloud, use the **PlayGamesPlatform.UpdateState**
method.

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    using GooglePlayGames.BasicApi;
    public class MyClass : OnStateLoadedListener {
        void SaveState() {
            // serialize your game state to a byte array:
            byte[] mySaveState = ...;
            int slot = 0; // slot number to use
            ((PlayGamesPlatform) Social.Active).UpdateState(slot,
                mySaveState, this);
        }
        public void OnStateSaved(bool success, int slot) {
            // handle success or failure
        }
        ...
```

The **OnStateSaved** method of the **OnStateLoadedListener** will be called to
indicate the success or failure of the cloud save operation.

## Loading Game State from the Cloud (only on Android)

To load game state from the cloud, use the **PlayGamesPlatform.LoadState**
method:<br/>

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    using GooglePlayGames.BasicApi;
    public class MyClass : OnStateLoadedListener {
        void LoadState() {
            int slot = 0; // slot number to use
            ((PlayGamesPlatform) Social.Active).LoadState(slot, this);
        }
        public void OnStateLoaded(bool success, int slot, byte[] data) {
            if (success) {
                // do something with data[]
            } else {
                // handle failure
            }
        }
        ....
```

## Resolving State Conflicts

A conflict happens when a device attempts to save state to the cloud but the
data currently on the cloud was written by a different device. When this
condition occurs, the OnStateConflict method of your OnStateLoadedListener will
be called, and you choose between (or merge) the two states and return a new
byte array representing the resolved state:<br/>

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;
    using GooglePlayGames.BasicApi;
    public class MyClass : OnStateLoadedListener {
        public byte[] OnStateConflict(int slot, byte[] local, byte[] server) {
            // resolve conflict and return a byte[] representing the
            // resolved state.
        }
    }
```

## Multiplayer

If you wish to integrate **turn-based multiplayer** in your game, refer to the
[Getting Started with Turn-Based Multiplayer](TBMP.md).

If you wish to integrate **real-time multiplayer** in your game, refer to the
[Getting Started with Real-Time Multiplayer](RTMP.md).

## Sign out

To sign the user out, use the **PlayGamesPlatform.SignOut** method.

```csharp
    using GooglePlayGames;
    using UnityEngine.SocialPlatforms;

    // sign out
    ((PlayGamesPlatform) Social.Active).SignOut();
```

After signing out, no further API calls can be made until the user authenticates again.


## Building for Android

To build your game for Android, do as you would normally do in Unity. Select
**File | Build Settings**, then select the **Android** platform and build. If
you are signing your APK file, please make sure that you are signing it with the
correct certificate, that is, the one that corresponds to the SHA1 certificate
fingerprint you entered in the Developer Console during the setup.


## (Advanced) Using the Plugin Without Overriding the Default Social Platform

When you call `PlayGamesPlatform.Activate`, Google Play Games becomes your default social platform implementation, which means that static calls to methods in `Social` and `Social.Active` will be carried out by the Google Play Games plugin. This is the desired behavior for most games using the plugin.

However, if for some reason you wish to keep the default implementation accessible (for example, to use it to submit achievements and leaderboards to a different social platform), you can use the Google Play Games plugin without overriding the default one. To do this:

1. Do not call `PlayGamesPlatform.Activate`
2. If `Xyz` is the name of a method you wish to call on the `Social` class, do not call `Social.Xyz`. Instead, call `PlayGamesPlatform.Instance.Xyz`
3. Do not use `Social.Active` when interacting with Google Play Games. Instead, use `PlayGamesPlatform.Instance`.

That way, you can even submit scores and achievements simultaneously to two or more social platforms:

    // Submit achievement to original default social platform
    Social.ReportProgress("MyAchievementIdHere", 100.0f, callback);

    // Submit achievement to Google Play
    PlayGamesPlatform.Instance.ReportProgress("MyGooglePlayAchievementIdHere", 100.0f, callback);


## Special Thanks

This section lists people who have contributed to this project by writing code, improving documentation or fixing bugs.

* [Dgizusse](https://github.com/Dgizusse) for figuring out that setting JAVA_HOME is necessary on Windows.
* [antonlicht](https://github.com/antonlicht) for fixing a bug with the parameter type of showErrorDialog on the support library.
* [pR0Ps](https://github.com/pR0Ps) for fixing an issue where OnAchievementsLoaded was not accepting an OPERATION_DEFERRED result code as a success.
* [friikyeu](https://github.com/friikyeu) for helping debug [an issue](https://github.com/playgameservices/play-games-plugin-for-unity/issues/25) that caused API calls to be queued up rather than executed even when connected.

