# Optimal In-App Remote for Android

Optimal In-App Remote SDK for Android は、 Android アプリの遠隔支援を実現するための開発キットです。
この SDK をアプリに組み込むことで、そのアプリを [Optimal Remote](http://www.optim.co.jp/products-detail/top/40) で遠隔支援できるようになります。

[iOS 版もあります。](https://github.com/optim-corp/optimal-in-app-remote-ios-sdk)

## 対象環境
  - SDK 動作環境
    1. Android 2.3、 Android 4.0 〜 7.0
    2. 英語、日本語
      - デフォルト言語は英語です
    3. インターネットに接続できるネットワーク環境
  - 開発環境
    - [ADT(Android Developer Tools)](http://developer.android.com/tools/help/adt.html) 導入済み Eclipse

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

### 2. SDK を Eclipse でインポートしてください
インポートは、次の通りです。 (File) -> (Import) -> (Android) -> (Existing Android Into Workspace) で Import Projects ダイアログを開きます。(Browse...) で先ほど取得または展開したディレクトリを指定してください。最後に (Finish) を押すとインポートが完了します。
SDK には、ライブラリファイル、レイアウトファイル、文字列ファイルと画像ファイルが含まれています。

### 3. 対象のアプリケーションに SDK を関連付けしてください
関連付けるには、対象のアプリケーションを右クリック、 (Properties) -> (Android) -> (Add...) で SDK ディレクトリを選択し、OK ボタンを押してください。

## SDK を利用するためのチュートリアル

### 1. AndroidManifest.xml の設定を行います
ネットワーク及びマイクの使用権限の設定します。また、`jp.co.optim.optimalremote.ORIAApplication` を application 要素の android:name　属性に設定してください。
オペレーターとの接続を管理する ORIASession インスタンスは `android.app.Application` クラスを継承した `jp.co.optim.optimalremote.ORIAApplication` から取得します。

```AndroidManifest.xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
  ...
  <!-- 1. オペレーターとの接続に使用します。 -->
  <uses-permission android:name="android.permission.INTERNET" />

  <!-- 2. VoIP 機能を有効にするために使用します。 -->
  <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
  <uses-permission android:name="android.permission.RECORD_AUDIO" />
  ...

  <!-- 3. ORIASession を取得するために android:name 属性を次のように設定してください -->
  <application
    android:name="jp.co.optim.optimalremote.ORIAApplication"
    ...

    <!--
    4. アクティビティに対して android:configChanges 属性を次のように設定してください。
    設定していない場合は、画面回転時に SDK 側から表示されていたダイアログが非表示になります。
     -->
    <activity
      android:configChanges="orientation|screenSize"
      ...

</manifest>
```


### 2. ボタンをクリックするオペレーターとの接続を開始させます
ここでは、ボタンをクリックするとオペレーターとの接続を開始する例を示します。 初めに、`MainActivity` の `onCreate` メソッド中で接続準備を行います。次に、ボタンをクリックするとオペレーターとの接続を開始するコードを実装します。　

```MainActivity.java
...
// 5. SDK を利用するために、それぞれインポートします。
import jp.co.optim.optimalremote.ORIAApplication;
import jp.co.optim.optimalremote.ORIASession;
import jp.co.optim.optimalremote.IORIASessionCallback;
...

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

  ...
}
```

### 3. オペレーターツールと接続する
アプリをビルドしたら、インターネットに接続された端末でアプリを実行し、ボタンをクリックすると「受付番号」が表示されます。オペレーターツールでこの受付番号を入力すると、オペレーターツールとアプリが接続され、オペレーターツールにアプリの画面が表示されます！

以上でチュートリアルは完了です。うまくオペレーターツールと接続できない場合、お問い合わせください。
