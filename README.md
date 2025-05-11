# iOS App Distribution with Fastlane & Firebase

This project uses [Fastlane](https://fastlane.tools/) to automate the process of building an iOS app and distributing it to beta testers using **Firebase App Distribution**.

## Platform

- **iOS**

## Purpose

This Fastlane configuration defines a custom lane `distribute` which:
1. Builds the iOS app using Xcode.
2. Exports the `.ipa` file with a specified provisioning profile and export method.
3. Uploads the build to Firebase App Distribution and notifies the testers.

## Fastlane Lane: `distribute`

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
          "com.aait.abr" => "com.aait.abr AppStore"
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
````

### Breakdown

#### `build_app(...)`

* **scheme**: The scheme used to build the app (typically your app's name, here it's `Runner`).
* **archive\_path**: Where the archive will be saved.
* **export\_method**: The method for exporting (e.g., `app-store`, `ad-hoc`, `development`, etc.).
* **output\_directory**: Where the `.ipa` file and other build artifacts will be saved.
* **export\_options.provisioningProfiles**: Specifies the provisioning profile to use for the app bundle ID.

#### `firebase_app_distribution(...)`

* **app**: Firebase App ID for the iOS app.
* **testers**: A comma-separated list of emails to notify testers.
* **firebase\_cli\_token**: The token used for authenticating the Firebase CLI (keep this secure).
* **release\_notes**: Notes to display in the release email.
* **firebase\_cli\_path**: Full path to the installed Firebase CLI.
* **ipa\_path**: Path to the generated `.ipa` file to upload.

## Prerequisites

* Install [Fastlane](https://docs.fastlane.tools/getting-started/ios/setup/)
* Install [Firebase CLI](https://firebase.google.com/docs/cli)
* Authenticate Firebase CLI and generate a token if needed:

  ```bash
  firebase login:ci
  ```

## Running the Lane

To build and distribute the iOS app, run:

```bash
fastlane ios distribute
```
