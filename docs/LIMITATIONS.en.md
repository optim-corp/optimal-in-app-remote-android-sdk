# Optimal In-App Remote SDK for Android: Limitations

## Basic limitations

1.  During showing a `Dialog`, the finger indicator and the red marker will not be shown in the foreground. But the Operator Tool can do remote controlling.
2.  Screen sharing supports capturing a `Dialog`, but the transparent color outside the `Dialog` area is not reflected in the capture screen.
3.  Screen sharing does not support capturing a software keyboard. However, the operator can input characters from the operator tool.
4.  Tap operations outside the application area, such as the software keyboard area or notification area, are not supported.
5.  `SurfaceView` or a `View` inheriting from it cannot be captured. It will be displayed as a solid color on the operator tool.
6.  This SDK only works on ARM architecture CPUs.
7.  There are some devices that stop the application process when the home button is pressed, regardless of whether this SDK is being used.
8.  Even if the [half-width characters/full-width characters] key is pressed on the operator tool side, the keyboard input method of the target application will not be switched.
9.  For some devices, right-clicking while using the finger pointer will move the finger pointer to the upper left of the screen.
10. If the screen is rotated while using the finger pointer, the finger pointer will move to the upper left of the screen.
11. When the voice call microphone is disabled, reconnecting after the support connection is disconnected will cause the microphone to become enabled.
12. When an internet connection cannot be established, calling `ORIASession.open` will cause the "Connecting..." message to continue being displayed.
13. TextureView animation cannot be captured. The operator tool side will be displayed without animation.
14. On Android 13 or later, remote control is only available for Views within the Window type "Application windows" that the SDK-integrated app belongs to. Please refer to the following link for more information about "Application windows":
    - https://developer.android.com/reference/android/view/WindowManager.LayoutParams#type
