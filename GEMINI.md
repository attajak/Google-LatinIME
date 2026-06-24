# Google LatinIME - Project Context & Developer Guide

Welcome to the Google LatinIME codebase. This document serves as a comprehensive developer guide, detailing the project overview, system architecture, build workflows, and development conventions to assist AI agents and human developers alike in navigating and contributing to this project.

---

## 1. Project Overview

**LatinIME** is the default open-source keyboard application (Input Method Editor) developed for the Android Open Source Project (AOSP). It is a highly optimized, feature-rich IME supporting multilingual layout switching, suggestions, autocorrection, dictionary compression, and custom gesture-input decoding.

### Core Architecture & Technologies
1. **Java & Android Framework (UI/Controller):** Handles the Android IME lifecycle, user interface interaction, keyboard key-press event dispatch, accessibility, and configuration settings.
   - **Main Entry Point:** `com.android.inputmethod.latin.LatinIME` (extends `android.inputmethodservice.InputMethodService`).
2. **C++ & JNI (Performance Engine):** Native binaries under `native/` execute low-latency tasks such as word traversal, spatial-model score computation, gesture typing prediction, and dictionary reading.
   - **Library name:** `libjni_latinime.so`.
3. **Hybrid Build Support:** 
   - **Gradle (`build.gradle`):** Enables stand-alone compilation, local testing, and developer iteration outside of a full AOSP check-out. Includes automated NDK integration via `externalNativeBuild`.
   - **Soong / Blueprint (`Android.bp`):** Facilitates direct compilation as a platform module inside system-level Android tree builds.

---

## 2. Directory Structure

```
Google-LatinIME/
├── java/                 # Main Android Application module
│   ├── src/              # App Java source code (com.android.inputmethod.latin)
│   ├── res/              # UI layout files, keyboard layouts, and locale values
│   ├── AndroidManifest.xml
│   └── proguard.flags    # Proguard obfuscation & optimization rules
├── common/               # Shared common Java libraries
│   └── src/              # Common Java utility classes
├── native/               # Native C++ core engine
│   ├── jni/              # JNI wrapper, source files (Android.mk & Android.bp)
│   └── dicttoolkit/      # Tooling and utilities for managing dictionaries
├── dictionaries/         # Compiled multilingual wordlist and dictionary packages
├── tests/                # Exhaustive instrumented and unit testing suite
│   └── src/              # Layout, input logic, gesture, and dictionary tests
└── tools/                # Auxiliary CLI and developer tools
    ├── dicttool/         # Java CLI program to package, inspect, and encrypt binary dicts
    ├── make-keyboard-text/ # Automated keyboard resource/text generator
    └── EditTextVariations/ # Testing utility containing diverse input configurations
```

---

## 3. Building and Running

Depending on your environment (stand-alone or as part of a full platform AOSP build), you can build and test LatinIME using different pipelines:

### Stand-alone Development (Gradle)

This is the preferred method for rapid local app-level iteration, UI tweaking, and testing on general emulators or devices.

*   **Build Debug APK:**
    ```bash
    ./gradlew assembleDebug
    ```
    *This compiles the Java code, automatically invokes the NDK build to generate `libjni_latinime.so` using `native/jni/Android.mk`, and packages them into a debug APK.*

*   **Run Local Unit Tests (Local JVM):**
    ```bash
    ./gradlew test
    ```

*   **Run Instrumentation Tests (Device/Emulator):**
    ```bash
    ./gradlew connectedAndroidTest
    ```

*   **Run Linter:**
    ```bash
    ./gradlew lint
    ```

*   **Clean Build Outputs:**
    ```bash
    ./gradlew clean
    ```

### AOSP Platform Development (Soong)

This is used for system-level integration or ROM development. Ensure you have sourced Android's environment setup (`source build/envsetup.sh` and targeted via `lunch`).

*   **Build LatinIME Module:**
    ```bash
    mmm packages/inputmethods/LatinIME/java
    ```

*   **Build Native C++ Engine:**
    ```bash
    mmm packages/inputmethods/LatinIME/native/jni
    ```

*   **Build Host Unit Tests (C++):**
    ```bash
    # Runs the native test suite on your local host (AOSP environment)
    ./native/jni/run-tests.sh --host
    ```

---

## 4. Development Conventions & Key Mechanisms

### A. The Input Method Service Lifecycle
`LatinIME.java` acts as the orchestrator. Important lifecycle methods include:
- `onCreate()`: Initializes system services, permissions managers, and loads native libraries via `JniUtils`.
- `onCreateInputView()`: Inflates and sets up the keyboard UI (`InputView` / `KeyboardActionListener`).
- `onStartInputView(EditorInfo, boolean)`: Resets state, determines the keyboard layout, configures autocorrect parameters, and re-initializes dictionaries according to the targeted input type.
- `onUpdateSelection(...)`: Tracks selection movements or text input events to update composition states.

### B. Uncompressed Binary Dictionaries (AAPT Optimization)
To optimize memory performance and boot times, dictionary files are mapped directly into virtual memory from the APK. 
- **Important:** To prevent the Android build system from compressing `.dict` files (which would make `mmap()` impossible at runtime), specialized flags are added to the build config:
  - **Gradle:** `aaptOptions { noCompress 'dict' }`
  - **Android.bp:** `aaptflags: ["-0 .dict"]`
- Ensure any new dictionary file matches this file extension to prevent compression and runtime crashes.

### C. JNI & C++ Bridge
All C++ files under `native/jni/` represent the functional bridge between high-level Java input views and low-level autocorrect prediction engines:
- `com_android_inputmethod_latin_BinaryDictionary.cpp` is the core JNI wrapper for suggestions.
- Native resources must be allocated and deallocated correctly inside matching Java handles to avoid memory leaks.

### D. Coding Conventions
- **Naming Style:** Standard Android convention (CamelCase, `m` prefix for member variables, `s` prefix for static variables).
- **Android Compatibility:** Kept compatible down to `minSdkVersion 21` (Android Lollipop) and compiled with `compileSdkVersion 35` using Java 8 syntax compatibility.
- **Resource Locales:** Keyboards use localized assets located inside `java/res/values-<locale>/` to load key definitions, symbols, and subtype dictionaries.

---

## 5. Development Tools

*   **`dicttool`:** Use this CLI to inspect the internal structures of `.dict` binary files, merge dictionaries, or package plain-text wordlists. It has its own mock environment for testing in `tools/dicttool/compat`.
*   **`EditTextVariations`:** Run this tool to test IME layouts and features across a wide catalog of Android `EditText` modes (e.g., password fields, email suggestions, custom action buttons, multilingual text, etc.).
