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
The project also supports standard Android Studio/Gradle builds via `build.gradle` and the `gradlew` wrapper.

**Note:** The provided Gradle wrapper (4.6) is quite old and may be incompatible with newer JDKs (e.g., JDK 21+). You may need to use an older JDK or attempt to update the wrapper if you intend to use Gradle for local development.

## Development Conventions

- **Hybrid Language:** Most UI/logic is in Java. Performance-sensitive dictionary and suggestion logic is implemented in C++ (exposed via JNI).
## Testing

LatinIME has a comprehensive suite of tests located in the `tests/` directory.

### Running Tests
The tests in this project are configured as **Instrumented Tests** (Android Tests), not JVM-based Unit Tests.

- **To run instrumented tests:**
  You must have a connected Android device or emulator.
  ```bash
  ./gradlew connectedDebugAndroidTest
  ```

- **Note on Unit Tests:**
  The project currently does not have standard JVM-based Unit Tests configured. The existing tests in `tests/` require an Android environment to run.

- **Licensing:** Adhere strictly to the project's license guidelines, ensuring all code additions maintain the Apache 2.0 license as specified in the `NOTICE` files and `Android.bp` configurations.
- **Project Structure:** Adhere to the existing directory conventions (e.g., `java/src/com/android/inputmethod/latin/...` for application code).
