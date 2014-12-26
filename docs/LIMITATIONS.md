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
 9. There are some devices which stop an application process when home button is pressed whether using this SDK or not. Please disable home button while remote assistance.
