# NovaHome – a Fire TV launcher

Dark, futuristic, remote-first home screen for the Fire TV Stick HD (Fire OS 7 / Android 9).
Pure Android framework + Kotlin, zero third-party libraries.

* Big tiles: Netflix, YouTube, Prime Video, Spotify (uses each app's own banner art when installed)
* Scrollable row with every other installed app
* Live clock + date, time-of-day greeting
* Animated drifting-light wallpapers (6 built in) + your own images
* Full D-pad navigation: scale-up + neon outline focus, smooth entrance & scroll animations
* Remote: arrows move, OK opens, MENU (≡) changes wallpaper, Back is ignored

## Build the APK

Requirements: Android Studio Hedgehog (2023.1) or newer. JDK 17 is bundled with it.

1. Android Studio -> File -> Open -> select the `NovaHome` folder. Let Gradle sync finish
   (it downloads Gradle 8.5 and the Android Gradle Plugin automatically).
2. Build -> Build Bundle(s) / APK(s) -> Build APK(s).
3. Output: `app/build/outputs/apk/debug/app-debug.apk`

Command line alternative (needs the Android SDK installed and a Gradle install once):

    gradle wrapper --gradle-version 8.5
    ./gradlew assembleDebug

For a release-type APK use `./gradlew assembleRelease` (it is signed with the debug key so it can be sideloaded).

## Install on the Fire TV Stick HD

1. Fire TV: Settings -> My Fire TV -> Developer Options -> turn on ADB debugging and Apps from Unknown Sources.
   (If Developer Options is missing: About -> click the device name 7 times.)
2. Find the IP: Settings -> My Fire TV -> About -> Network.
3. On your computer:

       adb connect <FIRE_TV_IP>:5555
       adb install -r app/build/outputs/apk/debug/app-debug.apk

   (Or host the APK and use the "Downloader" app on the Fire TV.)
4. Launch: Settings -> Applications -> Manage Installed Applications -> NovaHome -> Launch app.
   It also shows up in Your Apps & Channels.

### About the Home button
Fire OS hard-wires the Home button to Amazon's own launcher, and Amazon does not allow a normal
"choose default home app" prompt. NovaHome declares the HOME category, so tools that remap the
Home button (e.g. community "launcher manager" apps) can point Home at it, but results vary by
Fire OS version, so treat that as optional and at your own risk. Used as a normal app it works everywhere.

## Customising

* Wallpapers: copy JPG/PNG files named `wallpaper_1.jpg` ... `wallpaper_9.jpg` (1920x1080 works best)
  into `app/src/main/res/drawable-nodpi/` and rebuild. They appear after the built-in ones.
  Press MENU on the remote (or the picture icon at top-right) to cycle.
* Featured apps / package names: `featuredDefs` at the top of `MainActivity.kt`.
  If a tile says "Not installed" but the app is installed, add its package name to that list
  (`adb shell pm list packages | grep -i prime`).
* Colours: `Palette` in `Ui.kt`. Built-in wallpaper palettes: `WallpaperView.kt`.
* Size: all dimensions use a "vw" unit (percent of screen width) so it looks the same at 720p and 1080p.

## Project layout

    app/src/main/java/com/nova/tvlauncher/
        MainActivity.kt      screen layout, app loading, actions
        FocusCard.kt         focusable rounded card (scale + glow + lift animation)
        WallpaperView.kt     animated gradient / image wallpapers with cross-fade
        SmoothScrollView.kt  centre-on-focus horizontal scrolling
        GlyphView.kt         header icons drawn in code
        Ui.kt                colours + vw helpers
    app/src/main/AndroidManifest.xml   HOME + LEANBACK_LAUNCHER, no touchscreen required
