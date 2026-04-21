## <img width="64" alt="Quiet You! icon" src="https://github.com/user-attachments/assets/dae7a276-ae64-4fb4-ae74-48bc3efcedde" /> Quiet You!

This is a straight forward macOS app that automatically closes any notification that you want. It exists purely because there are a bunch of notifications in macOS that you can't turn off. (Those incessant "Background Items Added" notifications are the reason I made this app in the first place.)

You configure it by providing a list of text strings, and any time a notification pops on screen, the app will check to see if any bit of text in the notification contains any one of the text strings you added to the app. If there's a match, it will close the notification immediately.

Unfortunately there's no good way to stop the notification from appearing at all, but this way it's at least only on screen for a split second.

This app requires macOS 13 or later. It also requires Accessibility permissions in order to function. It should prompt for that when you first enable the app.


### How it works (the technical details)

It uses the Accessibility API and observers to be notified of when new UI elements appear in the Notification Center's list of notifications. It then finds all the labels in the notification and checks to see if they contain matching text, and upon finding a match sends the annoyingly hard to click X button on the notification a "press" message. This should be equivalent of actually clicking the X button but without needing to generate any actual mouse clicks. And since there's no polling it should have a very low CPU, memory and energy impact.


#### Possible future work:

Figuring out how to inject code into the Notification Center to actually prevent unwanted notifications from ever opening in the first place, for those sufficiently brave, desperate, or foolish. (I'm at least two of the three.)


## Fork Notes

This fork updates Quiet You! to be more robust on macOS Sequoia by making the Accessibility tree walking and notification dismissal logic less dependent on the older Notification Center hierarchy.

It has been tested on macOS Sequoia 15.7.3 against the recurring `Background Items Added` / `GoogleUpdater` notification.

This fork also adds a fallback for local unsigned builds: if the standard `SMAppService` helper registration fails because the app is not signed with a suitable Apple certificate, the app can fall back to installing a per-user `launchctl` LaunchAgent so the helper still runs locally for testing.


## Building Locally

You do not need a paid Apple Developer account to build and run this app locally on your own Mac.

What you do need:

- Xcode installed at `/Applications/Xcode.app`
- Accessibility permission for the helper app after launch

Build from Terminal:

```bash
xcodebuild -project QuietYou.xcodeproj -scheme QuietYou -configuration Release \
  CODE_SIGNING_ALLOWED=NO CODE_SIGNING_REQUIRED=NO CODE_SIGN_IDENTITY="" build
```

The built app will be placed under Xcode's DerivedData folder, typically at:

```text
~/Library/Developer/Xcode/DerivedData/.../Build/Products/Release/QuietYou.app
```

If you want to run it from Xcode instead, open `QuietYou.xcodeproj`, select the `QuietYou` scheme, and run the app normally.


## Running Locally

1. Launch `QuietYou.app`
2. Add one or more match strings such as `Background Items Added` or `GoogleUpdater`
3. Enable the checkbox to start the helper
4. If prompted, grant Accessibility access to the helper app

For unsigned local builds, macOS may require you to use right-click `Open` the first time you launch the app.


## Release Note

You need to build this fork yourself because I do not have the Apple Developer account setup needed to produce a macOS app build for distribution.
