name: Build Sam Android APK

on:
  workflow_dispatch:
  push:
    paths:
      - "project.zip"
      - ".github/workflows/build-apk.yml"

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Java 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "17"

      - name: Set up Android SDK
        uses: android-actions/setup-android@v3

      - name: Install Android platform and build tools
        run: sdkmanager "platforms;android-35" "build-tools;35.0.0"

      - name: Extract Android project
        run: |
          mkdir -p source
          unzip -q project.zip -d source

      - name: Set up Gradle
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: "8.9"

      - name: Build debug APK
        working-directory: source/Sam_Un_Universo_Griego
        run: gradle --no-daemon --stacktrace assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: Sam-Un-Universo-APK
          path: source/Sam_Un_Universo_Griego/app/build/outputs/apk/debug/app-debug.apk
          if-no-files-found: error
          retention-days: 7
