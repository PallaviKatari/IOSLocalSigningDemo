# Local iOS Signing

This project supports **local iOS code signing**, allowing you to build and install the app on a physical device without using App Store or TestFlight distribution.

Local signing is useful for:
- Internal testing
- Development builds
- CI-free local deployment
- Sideloading apps onto personal devices

---

## Requirements

Before you begin, ensure the following tools are installed:

- macOS
- Xcode (latest recommended)
- Xcode Command Line Tools
- Apple Developer Account (Free or Paid)
- iOS device connected via USB

Optional but recommended:

- `ios-deploy`
- `xcpretty`

Install optional tools:

```bash
brew install ios-deploy xcpretty

-------------

# Android Local Signing

This project supports **local Android code signing**, allowing you to build a **signed APK or AAB** directly on your machine without using external signing services.

Local signing is useful for:

* Internal testing
* Manual APK distribution
* Offline builds
* CI-independent release builds

---

# Requirements

Before building the project, ensure the following are installed:

* Android Studio
* Android SDK
* JDK 11 or newer
* Gradle (comes with Android Studio)

Verify installations:

```bash
java -version
gradle -v
```

---

# Project Setup

Clone the repository:

```bash
git clone <repository-url>
cd <project-folder>
```

Open the project in Android Studio.

---

# Create a Keystore

A keystore is required to sign the Android app.

Run:

```bash
keytool -genkey -v \
-keystore release.keystore \
-alias release \
-keyalg RSA \
-keysize 2048 \
-validity 10000
```

You will be prompted to enter:

* Keystore password
* Key password
* Name / Organization
* Country code

Store the generated file safely:

```
android/release.keystore
```

---

# Configure Signing in Gradle

Add the keystore configuration inside:

```
android/app/build.gradle
```

Example:

```gradle
android {
    signingConfigs {
        release {
            storeFile file("release.keystore")
            storePassword "YOUR_STORE_PASSWORD"
            keyAlias "release"
            keyPassword "YOUR_KEY_PASSWORD"
        }
    }

    buildTypes {
        release {
            signingConfig signingConfigs.release
            minifyEnabled false
        }
    }
}
```

---

# Build Signed APK

Run:

```bash
./gradlew assembleRelease
```

Output:

```
app/build/outputs/apk/release/app-release.apk
```

---

# Build Android App Bundle (AAB)

For Play Store distribution:

```bash
./gradlew bundleRelease
```

Output:

```
app/build/outputs/bundle/release/app-release.aab
```

---

# Install APK to Device

Connect your Android device via USB and enable **USB Debugging**.

Install APK:

```bash
adb install app/build/outputs/apk/release/app-release.apk
```

---

# Verify Signature

You can verify the APK signature:

```bash
jarsigner -verify -verbose -certs app-release.apk
```

---

# Security Notes

Never commit these files:

```
release.keystore
keystore.properties
```

Add them to `.gitignore`.

Example:

```
*.keystore
keystore.properties
```

---

# Troubleshooting

## Gradle Permission Error

Run:

```bash
chmod +x gradlew
```

---

## Keystore Not Found

Verify the path in `build.gradle`:

```
storeFile file("release.keystore")
```

---

## Device Not Detected

Check connected devices:

```bash
adb devices
```

Restart ADB if needed:

```bash
adb kill-server
adb start-server
```

---

# License

MIT
