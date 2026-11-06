# Repository Guidelines

LatinIME is an Android input method with Java application code and native C++
components. Use the existing Gradle workflow for standalone development and
preserve the repository's AOSP build metadata when changing shared code.

## Project structure

Application logic, resources, and the manifest live in `java/`; shared Java
utilities are in `common/`. Native JNI code is in `native/jni/`, with dictionary
toolkit code in `native/dicttoolkit/`. Instrumented Android tests and their
resources are in `tests/`. Supporting dictionary and keyboard tools are under
`tools/`, and compressed dictionary assets are in `dictionaries/`. Soong build
definitions are named `Android.bp` throughout the tree.

## Build and development

Use JDK 21 and an Android SDK/NDK installation compatible with the versions in
`build.gradle`. Run `./gradlew assembleDebug` to build the debug APK; this is
also the command used by CI. Run `./gradlew lintDebug` for Android lint. Run
`./gradlew connectedDebugAndroidTest` to execute instrumented tests on a
connected device or emulator. Use the Gradle wrapper so builds use the checked
in Gradle version.

## Style and naming

Match the surrounding Java, C++, XML, and Gradle conventions. Keep Java packages
under `com.android.inputmethod` and mirror package paths beneath source roots.
Name Java test classes after the behavior or class under test, typically with
`Tests` or `Test` suffixes. Keep native test names descriptive and ending in
`_test.cpp`. Preserve resource and locale naming patterns, especially in
`java/res/` and `dictionaries/`.

## Testing

Android application tests use the AndroidX instrumentation runner and JUnit;
they are primarily located in `tests/src/`. Run them with
`./gradlew connectedDebugAndroidTest` and include relevant native or dictionary
tool tests when changing those components. Confirm an Android device or
emulator is available before running connected tests.

## Commits and pull requests

Recent commits use short conventional subjects such as `build: ...`,
`chore: ...`, and `docs: ...`. Follow that pattern with an imperative summary.
For pull requests, describe the user-visible or build impact, link related
issues when available, and include test results. Add screenshots only when a
change affects visible keyboard or settings UI.
