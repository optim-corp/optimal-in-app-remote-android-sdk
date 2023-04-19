# Optimal In-App Remote SDK for Android: Limitations

## Basic limitations
 1. During showing a `Dialog`, the finger indicator and the red marker will not be shown in the foreground. But the Operator Tool can do remote controlling.
 2. Screen sharing supports a `Dialog` but not the shadow of a `Dialog`.
 3. Screen sharing does not support a software keyboard but operator can input characters by a keyboard.
 4. Screen sharing does not support a `SurfaceView` or a `View` inheriting from it.
 5. If `ORIASession.setRemoteInputEnabled` is called with `false`, remote controlling buttons ain the operation tool will be not gray out.
 6. After reconnected, the remote controlling will not be enabled. In this case, enable the remote controlling again.
 7. Remote controlling does not support out of the application area. (ex. Area of software keyboard, notification area.
 8. This SDK works on the ARM architecture.
 9. There are some devices which stop an application process when home button is pressed whether using this SDK or not.
 10. Even if the [half-width characters/full-width characters] button is toggled on the operator tool side, the keyboard input method of the target application will not change.
 11. For each device, right cliking while using the pointer finger will move the finger pointer to the upper left of the screen.
 12. If the device is rotated while using the pointer finger, the finger pointer will move to the upper left of the screen.
 13. When the remote control function is available, calling `"ORIASession.resume"` after calling `"ORIASession.pause"` will cause the remote control function to become disabled.
 14. When the voice call microphone is disabled, calling `"ORIASession.resume"` after calling `"ORIASession.pause"` will cause the microphone to become available.
 15. If `"ORIASession.resume"` is called one minute after `"ORIASession.pause"` is called, the connection confirmation dialog will be displayed.
 16. If calling `"ORIASession.pause"` interrupts the connection, calling `"ORIASession.shutdown"` will change `"ORIASession.canOpen"` to false.
 17. When an internet connection cannot be established, calling `"ORIASession.open"` will cause the "Connecting..." message to continue being displayed.
 18. The target application will terminate if it is disconnected from the internet while receiving remote support in the background.
 19. Cannot capture TextureView animation. The operator's side will be displayed without animation.
 20. On Android 13 or later, remote control doesn't work due to OS limitations.
 21. When you publish app on Google Play Store, [a warning about vulnerability of WebRTC](https://support.google.com/faqs/answer/12577537) will be displayed on Google Play Console.
   - This library is not affected by the vulnerability because the feature has vulnerability is unused in it.