### ADB shell on physical (not AVD) Android handset via USB

1. On Android Oreo tap `Settings` > `About phone` > `Software information`
2. Tap `Build number` 7 times until the `You are now X steps away from being a developer.` message disappears. You should see the following message pop: `Developer mode has been turned on.`
3. A new menu item should now be available under `Settings` > `Developer options`
4. Toggle `USB debugging` within `Developer options`. Tap `OK` in the `Allow USB debugging?` prompt
5. Connect Android handset to one of your Mac/PC's USB ports
6. Select `Always allow from this computer` and tap `OK` in the `Allow USB debugging?` prompt
7. Execute an `adb devices` command on the computer to which the Android handset is connected. E.g.
```
$ ~/android_sdk/platform-tools/adb devices
List of devices attached
1234567890123456	device
```
8. If a device is shown in the command's output, similar to the above example, issuing a `adb shell` command should succeed:
```
$ ~/android_sdk/platform-tools/adb shell
starqlteue:/ $
```
