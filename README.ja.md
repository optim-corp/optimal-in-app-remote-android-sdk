# Optimal In-App Remote for Android

Optimal In-App Remote SDK for Android は、 Android アプリの遠隔支援を実現するための開発キットです。
この SDK をアプリに組み込むことで、そのアプリを [Optimal Remote](http://www.optim.co.jp/products-detail/top/40) で遠隔支援できるようになります。

[iOS 版もあります。](https://github.com/optim-corp/optimal-in-app-remote-ios-sdk)

## 対象環境

- SDK 動作環境
  1. Android 5.0 〜 14.0
  2. 英語、日本語
  - デフォルト言語は英語です
  3. インターネットに接続できるネットワーク環境
- 開発環境
  - Android Studio

## この SDK でできること

### 画面共有機能

SDK を組み込んだアプリの画面をオペレーターがリアルタイムに閲覧できます。

### 遠隔操作機能

SDK を組み込んだアプリをオペレーターが操作できます。

### 赤ペン機能

SDK を組み込んだアプリの画面にオペレーターが赤ペンで書き込むことでユーザーに操作を指示することができます。

### 指さし機能

SDK を組み込んだアプリの画面にオペレーターから指マークを表示することでユーザーに操作を指示することができます。

### 音声通話機能

オペレーターがユーザーと VoIP で音声通話しながら遠隔支援を効果的に実施することができます。

## SDK をプロジェクトに導入する

以下の手順を進める前に、開発者登録をしていただき以下をご用意ください。

[詳しい手順はこちらを参照してください。](docs/REGISTRATION.ja.md)

1. SDK を利用するためのプロファイル・キーペア
2. オペレーターツール (Windows 版)
3. オペレーターツールを利用するためのアカウント (ID・パスワード)

### 1. この Git リポジトリをチェックアウトしてください

Git でチェックアウトしてください。アーカイブファイルとしてダウンロードする場合は、展開してください。

### 2. SDK をインポートしてください

#### 依存ライブラリに SDK を追加します

対象アプリケーションに SDK 及びその依存ライブラリを追加するために、 build.gradle へ次のコードを追記します。

```gradle
dependencies {
    implementation files("path/to/optimal_remote.aar")
    implementation "com.squareup.okhttp3:okhttp:4.11.0"
}
```

##### AndroidManifest.xml の設定をします

SDK の AndroidManifest.xml に含まれる要素が、対象アプリケーションと競合する場合がありますので、対処が必要です。

対象アプリケーションの AndroidManifest.xml へ、 `xmlns:tools="http://schemas.android.com/tools"` 属性を `manifest` 要素へ、`tools:replace="android:theme,android:allowBackup"` 属性を `application` 要素へ追記してください。

```xml
<manifest package="com.example.your_app"
          xmlns:android="http://schemas.android.com/apk/res/android"
          xmlns:tools="http://schemas.android.com/tools">

    ...

    <application
        tools:replace="android:theme,android:allowBackup"
    ...
</manifest>
```

#### 難読化の設定をします

難読化を有効にしているプロジェクトの場合は、 Proguard の設定ファイルに以下の内容を追記してください。

```
-keep class jp.co.optim.** { *; }
-keep class org.webrtc.** { *; }
```

## SDK を利用するためのチュートリアル

### 1. AndroidManifest.xml の設定を行います

ネットワーク及びマイクの使用権限の設定します。また、`jp.co.optim.optimalremote.ORIAApplication` を application 要素の android:name 　属性に設定してください。
オペレーターとの接続を管理する ORIASession インスタンスは `android.app.Application` クラスを継承した `jp.co.optim.optimalremote.ORIAApplication` から取得します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
  ...
  <!-- 1. オペレーターとの接続に使用します。 -->
  <uses-permission android:name="android.permission.INTERNET" />

  <!-- 2. VoIP 機能を有効にするために使用します。 -->
  <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
  <uses-permission android:name="android.permission.RECORD_AUDIO" />
  ...

  <!-- 3. ORIASession を取得するために android:name 属性を次のように設定してください。 -->
  <application
    android:name="jp.co.optim.optimalremote.ORIAApplication"
    ...
  >
    <!--
    4. アクティビティに対して android:configChanges 属性を次のように設定してください。
    設定していない場合は、画面回転時に SDK 側から表示されていたダイアログが非表示になります。
     -->
    <activity
      android:configChanges="orientation|screenSize"
      ...
    />
  </application>

</manifest>
```

### 2. ボタンをクリックするオペレーターとの接続を開始させます

ここでは、ボタンをクリックするとオペレーターとの接続を開始する例を示します。 初めに、`MainActivity` の `onCreate` メソッド中で接続準備を行います。次に、ボタンをクリックするとオペレーターとの接続を開始するコードを実装します。

<details open>
<summary>Kotlin</summary>

```kotlin
// ...
// 5. SDK を利用するために、それぞれインポートします。
import jp.co.optim.optimalremote.IORIASessionCallback
import jp.co.optim.optimalremote.ORIAApplication
import jp.co.optim.optimalremote.ORIASession
// ...

class MainActivity : Activity(), IORIASessionCallback, View.OnClickListener {
    // 6. ORIASession クラスをメンバーに追加します。
    private var mSession: ORIASession? = null
    // 7. オペレーター接続開始用のボタンをメンバーに追加します。
    private var mButtonHelp: Button? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        // 8. プロファイルを貼り付けます。
        val PROFILE = "XXXXXXXX"
        // 9. キーを貼り付けます。
        val KEY = "XXXXXXXX"

        // 10. オペレーターとの接続準備を行います。
        val app = application as ORIAApplication
        try {
            app.initSession(applicationContext, PROFILE, KEY)
        } catch (e: IllegalArgumentException) {
            e.printStackTrace()
            finish()
        } catch (e: RuntimeException) {
            e.printStackTrace()
            finish()
        }

        // 11. ORIASession インスタンスを取得します。
        mSession = app.session
        // 12. 接続の開始イベントと終了イベントのイベントリスナーを設定します。
        mSession?.setSessionCallback(this, Handler(mainLooper))
        // 13. VoIP を有効にする (既定では false)
        mSession?.setVoiceChatEnabled(true)

        // 14. 接続開始ボタンにイベントリスナーを設定します。
        mButtonHelp = findViewById(R.id.button_help) as Button
        mButtonHelp.setOnClickListener(this)
    }

    // 15. ボタンクリック時にオペレーターとの接続を開始します。
    override fun onClick(v: View) {
        if (v.id == R.id.button_help) {
            mSession?.open()
        }
    }

    // オペレーターとの接続処理を開始した時に呼び出されます。ボタンを無効にします。
    override fun onOpen() {
        mButtonHelp?.isEnabled = false
    }

    // オペレーターとの接続が終了した時に呼び出されます。ボタンを有効にします。
    override fun onComplete() {
        mButtonHelp?.isEnabled = true
    }

// ...
}
```

</details>

<details>
<summary>Java</summary>

```java
// ...
// 5. SDK を利用するために、それぞれインポートします。
import jp.co.optim.optimalremote.ORIAApplication;
import jp.co.optim.optimalremote.ORIASession;
import jp.co.optim.optimalremote.IORIASessionCallback;
// ...

public class MainActivity extends Activity implements IORIASessionCallback, OnClickListener {
    // 6. ORIASession クラスをメンバーに追加します。
    private ORIASession mSession = null;
    // 7. オペレーター接続開始用のボタンをメンバーに追加します。
    private Button mButtonHelp = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        // 8. プロファイルを貼り付けます。
        String PROFILE = "XXXXXXXX";
        // 9. キーを貼り付けます。
        String KEY = "XXXXXXXX";

        // 10. オペレーターとの接続準備を行います。　
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

        // 11. ORIASession インスタンスを取得します。
        mSession = app.getSession();
        // 12. 接続の開始イベントと終了イベントのイベントリスナーを設定します。
        mSession.setSessionCallback(this, new Handler());
        // 13. VoIP を有効にする (既定では false)
        mSession.setVoiceChatEnabled(true);

        // 14. 接続開始ボタンにイベントリスナーを設定します。
        mButtonHelp = (Button) findViewById(R.id.button_help);
        mButtonHelp.setOnClickListener(this);
    }

    // 15. ボタンクリック時にオペレーターとの接続を開始します。
    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.button_help){
            mSession.open();
        }
    }

    // オペレーターとの接続処理を開始した時に呼び出されます。ボタンを無効にします。
    @Override
    public void onOpen() {
        mButtonHelp.setEnabled(false);
    }

    // オペレーターとの接続が終了した時に呼び出されます。ボタンを有効にします。
    @Override
    public void onComplete() {
        mButtonHelp.setEnabled(true);
    }

// ...
}
```

</details>

### 3. オペレーターツールと接続する

アプリをビルドしたら、インターネットに接続された端末でアプリを実行し、ボタンをクリックすると「受付番号」が表示されます。オペレーターツールでこの受付番号を入力すると、オペレーターツールとアプリが接続され、オペレーターツールにアプリの画面が表示されます！

以上でチュートリアルは完了です。うまくオペレーターツールと接続できない場合、お問い合わせください。
