# Optimal In-App Remote SDK for Android

"Optimal In-App Remote SDK for Android" is a developer kit that provides remote assistance features for your Android app.
By incorporating this SDK into your Android application, you can provide remote assistance for your app using [Optimal Remote](https://www.optim.com/products-detail/top/40).

[iOS version is also available on GitHub.](https://github.com/optim-corp/optimal-in-app-remote-ios-sdk)

This documentation in Japanese is here: [日本語ドキュメント](README.ja.md)

##Operating environment

- Operating Environment for app
  1.  Android 5.0 - 14.0
  2.  English / Japanese environment
      - The default language is English.
  3.  Access to the Internet is required.
- Required development environment
  - Android Studio

## SDK features

### Screen sharing

SDK allows real-time sharing of your app's screen.

### Remote control feature

SDK allows your app to be operated by remote operators (help desks, customer support team, etc.).

### Highlighting

SDK allows remote operators to highlight and draw on the screen using a red marker, providing more efficient instructions to users.

### Finger indicator

SDK also allows remote operators to point to the specific area of the screen using a finger icon.

### Voice calls

SDK also provides VoIP voice calls between app user and remote operator for easier support.

## Incorporating SDK to your project

Before starting with the procedure below, register as a developer and make sure you have all the requirements listed below:

[Please refer here for the detail request steps.](docs/REGISTRATION.md)

1.  Profile and key pair required for SDK
2.  Optimal Remote Operator Tool (for Windows)
3.  User account (user ID and password) for using Optimal Remote Operator Tool

### 1. Checkout this git repository

When downloaded as a archiving file, expand it.

### 2. Import the SDK

Please import the SDK appropriate for your programming environment.

#### For Android Studio

Please implement the following settings to the target application's settings.gradle
For the "File" parameter, designate the path to `"optimal_remote"` in the SDK directory. For example, `"C:\\Users\\username\\optimal_remote"` or
`"/Users/username/optimal_remote"`.

```
include ':optimal_remote'
project(':optimal_remote').projectDir = new File('[A path to the optimal_remote]')
```

Next, to add the SDK to the target application's dependent library, add the following code to "build.gradle"

```
dependencies {
    api project(':optimal_remote')
}
```

The element with the SDK AndroidManifest.xml will conflict with the target application. The following edits are necessary.

##### How to edit the SDK AndroidManifest.xml

Delete the `android:theme` element and the `android:allowBackup` element from the `AndroidManifest.xml` file in the optimal_remote directory.

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="jp.co.optim.optimalremote"

    ...

    <application>
    </application>

</manifest>
```

##### How to edit the target application AndroidManifest.xml

In the AndroidManifest.xml file for the target application, add `"xmlns:tools="http://schemas.android.com/tools"` to the `"manifest"` element, and `"tools:replace="android:theme,android:allowBackup"` to the `"application"` element.

```
<manifest package="com.example.your_app"
          xmlns:android="http://schemas.android.com/apk/res/android"
          xmlns:tools="http://schemas.android.com/tools">

    ...

    <application
        tools:replace="android:theme,android:allowBackup"
    ...
</manifest>
```

## Tutorials for using SDK

Following describes sets of codes frequently used in apps utilizing this SDK.

### 1. Modify AndroidManifest.xml

Config use permissions of using network and microphone. And set `jp.co.optim.optimalremote.ORIAApplication` value with `android:name` attribute of application element.
`ORIASession` instance is gettable from `jp.co.optim.optimalremote.ORIAApplication` inheriting `android.app.Application` class.

```AndroidManifest.xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
...
  <!-- 1. For connect to operator. -->
  <uses-permission android:name="android.permission.INTERNET" />

  <!-- 2. For enabling VoIP -->
  <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
  <uses-permission android:name="android.permission.RECORD_AUDIO" />
  ...

  <!-- 3. Set android:name as the below value. -->
  <application
    android:name="jp.co.optim.optimalremote.ORIAApplication"
    ...
  >
    <!--
    4. Add the following uses-library tag in the application to communicate with the operator.
     -->
    <uses-library android:name="org.apache.http.legacy" android:required="false"/>
    <!--
    5. Set android:configChanges as below value in each activity.
    If not, a dialog from SDK will be disappeared when rotating screen.
    -->
    <activity
      android:configChanges="orientation|screenSize"
      ...

</manifest>
```

### 2. Start remote assistance when clicking button

This example shows how to implement starting remote assistance when clicking button.
At first, initialize remote assistance in `onCreate` method of `MainActivity`. Next, implement a button starts remote assistance by clicking.

```MainActivity.java
...
// 6. Import these for using SDK.
import jp.co.optim.optimalremote.ORIAApplication;
import jp.co.optim.optimalremote.ORIASession;
import jp.co.optim.optimalremote.IORIASessionCallback;
...

public class MainActivity extends Activity implements IORIASessionCallback, OnClickListener {
  // 7. Add a ORIASession member value.
  private ORIASession mSession = null;
  // 8. Add a Button member for starting remote assistance.
  private Button mButtonHelp = null;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    // 9. Copy and paste the content of ".profile" to replace "XXXXXXXX" below:
    String PROFILE = "XXXXXXXX";
    // 10. Copy and paste the content of ".key" to replace "XXXXXXXX" below:
    String KEY = "XXXXXXXX";

    // 11. Initialize connecting to operator.
    ORIAApplication app = (ORIAApplication) getApplication();
    try {
      app.initSession(getApplicationContext(), PROFILE, KEY);
    } catch (IllegalArgumentException e) {
      e.printStackTrace();
      finish();
    } catch (RuntimeException e) {
      e.printStackTrace();
      finish();
    }

    // 12. Get an ORIASessioan instance.
    mSession = app.getSession();
    // 13. Set an event listener of beginning remote assistance event and ending.
    mSession.setSessionCallback(this, new Handler());
    // 14. Enable VoIP (default: false)
    mSession.setVoiceChatEnabled(true);

    // 15. Set a click event listener.
    mButtonHelp = (Button) findViewById(R.id.button_help);
    mButtonHelp.setOnClickListener(this);
  }

  // 16. Start remote assistance when clicked.
  @Override
  public void onClick(View v) {
    if (v.getId() == R.id.button_help){
      mSession.open();
    }
  }

  // Disable the button. This method will be called when starting remote assistance.
  @Override
  public void onOpen() {
    mButtonHelp.setEnabled(false);
  }

  // Enable the button. This method will be called when finishing remote assistance.
  @Override
  public void onComplete() {
    mButtonHelp.setEnabled(true);
  }

  ...
}
```

### 4. Connecting to the Operator Tool

After building your app, run the app from the device with access to the Internet. Tap the button to display "Receipt Number". Enter this Receipt Number from Operator Tool. Operator Tool and App is connected and app screen is displayed on Operator Tool. Now you are ready to go!

This completes the tutorial for SDK. Please contact us if you experience problems connecting to the Operator Tool.
