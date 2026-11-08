name: Build SANKUR AI APK

on:
  workflow_dispatch:
  push:
    branches: [main, master]

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-24.04
    timeout-minutes: 90

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Java 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.47.9'
          channel: stable
          cache: true

      - name: Android SDK
        uses: android-actions/setup-android@v3

      - name: Install Android tools
        run: |
          yes | sdkmanager --licenses
          sdkmanager \
            "platforms;android-36" \
            "build-tools;36.0.0" \
            "ndk;27.0.12077973" \
            "cmake;3.22.1"

      - name: Check files
        run: |
          test -f pubspec.yaml
          test -f lib/main.dart
          test -f tool/patch_android.py
          python3 -m py_compile tool/patch_android.py

      - name: Generate Android project
        run: |
          flutter create --no-pub \
            --platforms=android \
            --project-name sankur_ai_ultra \
            --org com.sankur \
            --android-language kotlin .

      - name: Configure Android
        run: python3 tool/patch_android.py

      - name: Download dependencies
        run: flutter pub get

      - name: Build APK
        run: flutter build apk --release --target-platform android-arm64

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: SANKUR-AI-ULTRA-APK
          path: build/app/outputs/flutter-apk/app-release.apk
          if-no-files-found: error
          retention-days: 14
