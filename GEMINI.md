# LatinIME

LatinIME is the reference implementation of an Android Input Method Editor (IME) for AOSP (Android Open Source Project). It is a hybrid project containing both Java application logic and high-performance native (C++) code for dictionary lookups and word suggestions.

## Project Structure

- `java/`: Main Android application code, resources, and manifest.
- `native/`: Native (C++) JNI code for performance-critical components (`native/jni/`) and dictionary tools (`native/dicttoolkit/`).
- `common/`: Shared Java code used by the IME and potentially other components.
- `dictionaries/`: Pre-compiled dictionary data files in compressed format.
- `tests/`: Extensive unit and integration test suite.
- `tools/`: Various helper tools for dictionary and keyboard development.

## Building and Running

This project supports two build workflows:

### 1. AOSP Integration (Soong/Android.bp)
This is the primary build system for integrating LatinIME into the Android OS build. The project is managed via `Android.bp` files across the repository, utilizing the Soong build system.

### 2. Standard Android Development (Gradle)
The project supports standard Android Studio/Gradle builds via `build.gradle` and the `gradlew` wrapper (targeting Gradle 9.x / AGP 9.4.1).
- Uses AndroidX Preference (`PreferenceFragmentCompat`) for all settings fragments and screens.
- Java 21 compatibility with core library desugaring.

## Development Conventions

- **Hybrid Language:** Most UI/logic is in Java. Performance-sensitive dictionary and suggestion logic is implemented in C++ (exposed via JNI).
- **AndroidX & Modernization:** Use AndroidX components (`PreferenceFragmentCompat`, `AppCompatActivity`) for UI and settings.

## Testing

LatinIME has a comprehensive suite of tests located in the `tests/` directory.

### Running Tests
The tests in this project are configured as **Instrumented Tests** (Android Tests), not JVM-based Unit Tests.

- **To run instrumented tests:**
  You must have a connected Android device or emulator.
  ```bash
  ./gradlew connectedDebugAndroidTest
  ```

- **Licensing:** Adhere strictly to the project's license guidelines, ensuring all code additions maintain the Apache 2.0 license as specified in the `NOTICE` files and `Android.bp` configurations.
- **Project Structure:** Adhere to the existing directory conventions (e.g., `java/src/com/android/inputmethod/latin/...` for application code).
