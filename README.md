# MuinScreenTranslator

An Android app that translates text on your screen, over any app. Tap the floating bubble and the translations appear on top of the original text, in the same places.

Everything runs on the phone. Screenshots stay in memory and are never saved or uploaded, and once the language files are downloaded the app works offline.

**Download:** [`ci-output/MuinScreenTranslator.apk`](ci-output/MuinScreenTranslator.apk)

## Features

- A floating bubble you can drag anywhere. Tap it to translate the current screen.
- Text recognition (OCR) on the phone for **Russian, English and Arabic**. It uses Google ML Kit together with Tesseract. On phones where ML Kit is slow, the app switches to Tesseract alone.
- Translation on the phone with ML Kit, plus automatic detection of the source language.
- The translation overlay matches the original text's position, background and text color, and lays out Arabic right to left.
- A toolbar on the overlay to close, translate again, change the target language, or switch between original and translation.
- A notification with **Translate** and **Stop** actions.
- Handles screen rotation.
- Screens for onboarding, settings, model manager, privacy, about, and Xiaomi/MIUI help.
- The default target language is Arabic.

## Requirements

- Android 8.0 (API 26) or newer. The app targets Android 15 (API 35).
- ARM phones (arm64-v8a and armeabi-v7a).
- An internet connection the first time each language is used, to download its models (about 30 MB per translation language and 1 to 4 MB per OCR language).

## Using it

1. Install the APK and open **MuinScreenTranslator**.
2. Follow the onboarding. It asks for permission to display over other apps, notification permission, and screen-capture consent.
3. Optional: go to **Model manager** and download Arabic, Russian and English, so the app works offline.
4. Open any app, then tap the bubble. Tap the overlay to close it.

### Xiaomi / Redmi / POCO (MIUI, HyperOS)

Go to *Settings → Apps → MuinScreenTranslator* and allow these:

- **Display pop-up windows** and **Display pop-up windows while running in the background**
- **Autostart**
- Battery saver set to **No restrictions**

The in-app **Xiaomi help** screen opens these settings directly.

## Privacy and security

- The app takes a screenshot only when you tap the bubble or the notification action. It processes the screenshot in RAM and clears it right after. Screenshots are never written to storage and never uploaded.
- Recognized text is never logged. Translations are kept only in an in-memory cache.
- The internet is used only to download models. Cleartext HTTP is disabled.
- OCR language data is pinned to a specific tessdata_fast commit and verified by SHA-256 before it is used.
- ML Kit's usage-statistics uploader (Google datatransport) is removed in the manifest.
- The phone build is non-debuggable, and app backups are disabled.
- The app has no root, ADB, Shizuku or AccessibilityService requirements, and needs no paid API keys.

## Building

```bash
./gradlew assembleRelease      # app/build/outputs/apk/release/app-release.apk
./gradlew testReleaseUnitTest  # unit tests
```

The build uses AGP 8.10, Kotlin 2.1, Gradle 8.14, Jetpack Compose and JDK 17.

To sign with your own key, create a `keystore.properties` file in the project root with `storeFile`, `storePassword`, `keyAlias` and `keyPassword`. That file is git-ignored. Without it, the release build is signed with the debug key.

## CI

The workflow `.github/workflows/build-apk.yml` runs on every push. It:

1. Builds the APK and runs the unit tests and lint.
2. Runs an end-to-end test on an Android 14 emulator (`ci/emulator-test.sh`). The test covers Russian→Arabic, Arabic→English, English→Russian, rotation, and the stop flow, and checks that the app is not debuggable.
3. Commits the APK, screenshots and logs to `ci-output/`.

## Project layout

```
app/src/main/java/com/screentranslate/app/
  capture/      MediaProjection screen capture
  ocr/          ML Kit + Tesseract engines, merging, grouping, language data download
  language/     language detection
  translation/  ML Kit translation, LRU cache
  overlay/      bubble, translation overlay, toolbar
  service/      foreground capture service
  settings/     DataStore settings
  ui/           Compose screens
```
