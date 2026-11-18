

name: Build Zaivids Android APK

on:
  workflow_dispatch:
  push:
    branches:
      - main

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Get repository
        uses: actions/checkout@v4

      - name: Set up Node
        uses: actions/setup-node@v4
        with:
          node-version: 22

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 21

      - name: Extract Zaivids project
        run: |
          unzip -q zaivids-capacitor-starter.zip -d /tmp/zaivids
          cp -R /tmp/zaivids/zaivids-capacitor/. .

      - name: Install dependencies
        run: npm install

      - name: Create Android project
        run: npx cap add android

      - name: Sync Android project
        run: npx cap sync android

      - name: Build APK
        run: |
          cd android
          chmod +x gradlew
          ./gradlew assembleDebug

      - name: Save APK
        uses: actions/upload-artifact@v4
        with:
          name: Zaivids-Android-APK
          path: android/app/build/outputs/apk/debug/app-debug.apk
