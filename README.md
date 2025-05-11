# 🚀 Mobile App Deployment with Fastlane

This project uses [Fastlane](https://fastlane.tools/) to automate building and distributing your mobile apps for both **Android** and **iOS**, supporting:
- Firebase App Distribution (Android & iOS)
- TestFlight (iOS)

---

## 📦 Prerequisites

- Install [Fastlane](https://docs.fastlane.tools/getting-started/ios/setup/)
- Install Firebase CLI:  
  ```bash
  npm install -g firebase-tools
````

* Authenticate and generate a Firebase CLI token:

  ```bash
  firebase login:ci
  ```

---

## 🔧 Firebase App Distribution

### ✅ Android Lane

Distributes the Android APK using Firebase App Distribution.

```ruby
platform :android do
  desc "Build the Android app and distribute it to beta testers using Firebase App Distribution"
  gradle(
      task: 'assemble',
      build_type: 'Release'
  )
  lane :distribute do
      firebase_app_distribution(
          app: "1:640697085052:android:62660527d0335614516df9",
          testers: "ahmed.saleh212020@gmail.com, ahmed.saleh212022@gmail.com",
          firebase_cli_token: "token",
          release_notes: "Finish App",
          firebase_cli_path: "/usr/local/bin/firebase",
          android_artifact_type: "APK",
          android_artifact_path: "../build/app/outputs/apk/release/app-release.apk"
      )
  end
end
```

---

### ✅ iOS Firebase Distribution

Builds and distributes the iOS app using Firebase App Distribution.

```ruby
platform :ios do
  desc "Build the iOS app and distribute it to beta testers using Firebase App Distribution"
  lane :distribute do
    build_app(
      scheme: "Runner",
      archive_path: "./build/Runner.xcarchive",
      export_method: "app-store",
      output_directory: "./build/Runner",
      export_options: {
        provisioningProfiles: { 
          "com.aait.elmansak" => "com.aait.elmansak AppStore"
        }
      }
    )
    firebase_app_distribution(
      app: "1:640697085052:ios:c833a9003926dfc7516df9",
      testers: "ahmed.saleh212020@gmail.com, ahmed.saleh212022@gmail.com",
      firebase_cli_token: "token",
      release_notes: "Finish App",
      firebase_cli_path: "/usr/local/bin/firebase",
      ipa_path: "./build/Runner/Runner.ipa"
    )
  end
end
```

---

## 🍏 iOS TestFlight Deployment

Uploads a new version of the iOS app to TestFlight via App Store Connect.

```ruby
default_platform(:ios)

platform :ios do
  desc "Push a new beta build to TestFlight"
  lane :beta do
    increment_version_number(
        version_number: "1.0.1", # Set your desired version here
        xcodeproj: "Runner.xcodeproj"
    )
    increment_build_number(
      xcodeproj: "Runner.xcodeproj", 
      build_number: "3" # Set desired build number here
    )

    build_app(workspace: "Runner.xcworkspace", scheme: "Runner")

    api_key = app_store_connect_api_key(
      key_id: "Key ID",
      issuer_id: "Issuer ID",
      key_filepath: "./fastlane/AuthKey_NTBP6NWLU6.p8",
      duration: 1200,
      in_house: false
    )

    upload_to_testflight(
      api_key: api_key
    )
  end
end
```

---

## 🚀 Usage

### Distribute Android via Firebase

```bash
cd android
fastlane distribute
```

### Distribute iOS via Firebase

```bash
cd ios
fatlane distribute
```

### Upload iOS Build to TestFlight

```bash
cd ios
fastlane beta
```

---

## 🔐 Security Notes

* Replace `"token"` with your actual `firebase_cli_token`.
  It’s better to use an environment variable:

  ```ruby
  firebase_cli_token: ENV["FIREBASE_TOKEN"]
  ```
