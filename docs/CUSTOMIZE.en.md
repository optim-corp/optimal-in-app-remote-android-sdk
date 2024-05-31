# Customizing "Optimal In-App Remote SDK for Android" apps

### Remote control feature

Remote control from the remote operators is enabled by default.

With the default setting, when the remote control is requested from the operator, a dialog is displayed and asks user whether to allow remote control. According to user's choices, following actions take place.

- When "Allow" is selected
  - Operators are allowed to remotely control the device.
  - When the operator requests remote control access again, it is accepted automatically without dialog.
- When "Allow(Only once)" is selected
  - Operators are allowed to remotely control the device.
  - When the operator requests remote control access again, dialog is displayed.
- When "Deny" is selected
  - Operator is not allowed to remotely control the device.
  - When the operator requests remote control access again, dialog is displayed.

### Disabling remote control

If remote control operation from remote operators need to be disabled, call "setRemoteInputEnabled" method with "false" argument immediately after creating "ORIASession" class instance.
When the argument is set to "false", no dialog prompting for permission is displayed and no remote operation will be executed.

### Allowing remote control operation automatically without permission dialog

If remote control operation from remote operator needs to be allowed without permission dialog, call "setRemoteInputAcceptsAutomaticallyEnabled" with "true" argument immediately after creating "ORIASession" class instance. When the property is set to "YES", no dialog prompting for permission is displayed and remote operation will be allowed automatically.

### Voice call feature

Voice call with remote operators is disabled by default.

When voice call with remote operators is enabled, voice call session will start when the remote operator requests for voice call.

Icon is displayed during the voice call. Tapping icon displays a menu, which provides users with following options.

- Option to output audio from the speakers (Hands free mode)
  − Option to mute the microphone

### Enabling voice call

If voice call with remote operator needs to be allowed, call "setVoiceChatEnabled" with "true" argument immediately after creating "ORIASession" class instance.

### Use not ORIAApplication class but the other class inheriting with Application class

`ORIAApplication` inheriting with `android.app.Application` has one `ORIASession` instance for controlling the instance.
However, there is a limitation that Android application contains only one `android.app.Application` instance.
SDK provides an alternative method in case using other `android.app.Application` inherit class.

The following example is using a `MyApplication` class inheriting with `android.app.Application`.

```MyApplication.java
// 1. Import these classes.
import jp.co.optim.optimalremote.IORIASessionProvider;
import jp.co.optim.optimalremote.ORIASession;
import jp.co.optim.optimalremote.ORIASessionProvider;
import android.app.Application;
import android.content.Context;
...

// 2. Implement IORIASessionProvider.
public class MyApplication extends Application implements IORIASessionProvider {
  // 3. ORIASessionProvider implementation is same as ORIAApplication.
  private final ORIASessionProvider mSessionProvider = new ORIASessionProvider();

  ...

  @Override
  public void initSession(Context context, String profile, String key) {
    // 4. Initialize connecting to operator.
    mSessionProvider.initSession(context, profile, key);
  }

  @Override
  public ORIASession getSession() {
    // 5. Return ORIASession instance.
    return mSessionProvider.getSession();
  }

  ...
}

```

## To capture TextureView

`TextureView` capture is unvailable by default in the SDK. To enable capture, set the `"setCaptureTextureViewEnabled"` method to `"true."` To turn it off, set the method to `"false."`
Enabling capture for `TextureView` may affect the performance of the application.
The `"setCaptureTextureViewEnabled"` setting cannot be changed while conducting support. Confirm the setting before calling the `"open"` method of the `"ORIASession"` object.
