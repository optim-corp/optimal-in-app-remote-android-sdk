# Optimal In-App Remote SDK for Android

"Optimal In-App Remote SDK for Android" is a developer kit that provides remote assistance features for your Android app.
By incorporating this SDK into your Android application, you can provide remote assistance for your app using [Optimal Remote](http://en.optim.co.jp/contents/5480).

[iOS version is also available on GitHub.](https://github.com/optim-corp/optimal-in-app-remote-ios-sdk)

This documentation in Japanese is here: [日本語ドキュメント](README.ja.md)

##Operating environment
 - Operating Environment for app
     1. Android 2.3, Android 4.0 or later
     2. English / Japanese environment
         - The default language is English.
     3. Access to the Internet is required.
 - Required development environment
     1. Eclipse with [ADT(Android Developer Tools)](http://developer.android.com/tools/help/adt.html)

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

 1. Profile and key pair required for SDK
 2. Optimal Remote Operator Tool (for Windows)
 3. User account (user ID and password) for using Optimal Remote Operator Tool


### 1. Checkout this git repository
When downloaded as a archiving file, expand it.

### 2. Import as optimal_remote project by eclipse
The below is how to import optimal_remote project. Open "Import Projects" dialog by operating (File) -> (Import) -> (Android) -> (Existing Android Into Workspace) .
Specify the directory which is checkout or expanded from a archiving file. Finally, click (Finish) button and finish importing.
optimal_remote project contains as a set of a library file, layout files, string files and image files.

### 3. Add optimal_remote project to your android app project
Click (Properties) -> (Android) -> (Add...) ,select SDK directory and click OK button.

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

    <!--
    4. Set android:configChanges as below value in each activity.
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
// 5. Import these for using SDK.
import jp.co.optim.optimalremote.ORIAApplication;
import jp.co.optim.optimalremote.ORIASession;
import jp.co.optim.optimalremote.IORIASessionCallback;
...

public class MainActivity extends Activity implements IORIASessionCallback, OnClickListener {
  // 6. Add a ORIASession member value.
  private ORIASession mSession = null;
  // 7. Add a Button member for starting remote assistance.
  private Button mButtonHelp = null;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    // 8. Copy and paste the content of ".profile" to replace "XXXXXXXX" below:
    String PROFILE = "XXXXXXXX";
    // 9. Copy and paste the content of ".key" to replace "XXXXXXXX" below:
    String KEY = "XXXXXXXX";

    // 10. Initialize connecting to operator.
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

    // 11. Get an ORIASessioan instance.
    mSession = app.getSession();
    // 12. Set an event listener of beginning remote assistance event and ending.
    mSession.setSessionCallback(this, new Handler());
    // 13. Enable VoIP (default: false)
    mSession.setVoiceChatEnabled(true);

    // 14. Set a click event listener.
    mButtonHelp = (Button) findViewById(R.id.button_help);
    mButtonHelp.setOnClickListener(this);
  }

  // 15. Start remote assistance when clicked.
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
