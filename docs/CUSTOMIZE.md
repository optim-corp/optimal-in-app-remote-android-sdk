# Optimal In-App Remote SDK for Android アプリのカスタマイズ

## 遠隔操作機能

オペレーターから遠隔操作を要求された場合、既定ではそれを許可するかどうかのダイアログが表示され、ユーザーの選択結果に応じて以下のように振る舞います。

- 「許可」を選択した場合
  - オペレーターからの遠隔操作を受け入れるようになります
  - 再度遠隔操作を要求した場合、自動的に遠隔操作を受け入れます
- 「許可 (今回のみ)」を選択した場合
  - オペレーターからの遠隔操作を受け入れるようになります
  - 再度遠隔操作を要求した場合、ダイアログが表示されます
- 「拒否」を選択した場合
  - オペレーターからの遠隔操作を受け入れません
  - 再度遠隔操作を要求した場合、ダイアログが表示されます

### 遠隔操作機能を無効化する

オペレーターからの遠隔操作を無効にしたい場合、`ORIASession` クラスのインスタンスを生成した直後に `setRemoteInputEnabled` メソッドで引数に `false` を渡してください。`false` にすると、遠隔操作を許可するかどうかのダイアログは表示されず、遠隔操作も実行されません。

### 遠隔操作機能をダイアログなしで自動的に許可する

オペレーターからの遠隔操作をダイアログなしで自動的に許可したい場合、`ORIASession` クラスのインスタンスを生成した直後に `setRemoteInputAcceptsAutomaticallyEnabled` メソッドで引数を `true` を渡してください。 `true` にすると、遠隔操作を許可するかどうかのダイアログは表示されず、自動的に遠隔操作が許可されます。

## 音声通話機能

既定では、オペレーターとの音声通話が無効になっています。

オペレーターとの音声通話が有効になっている場合、オペレーターから音声通話を要求されると音声通話を開始します。

ユーザーは、オペレーターとの接続中に表示されるアイコンをタップすると開くメニューから、必要に応じて以下を調整できます。

- スピーカーから音声を出力するようにするかどうか (ハンズフリー)
  − マイクをミュートするかどうか

### 音声通話機能を有効化する

オペレーターとの音声通話を有効にしたい場合、`ORIASession` クラスのインスタンスを作成した直後に `setVoiceChatEnabled` メソッドで引数に `true` を渡してください。

### ORIAApplication クラスではなく他の Application 継承クラスを利用する

`ORIAApplication` は、 `android.app.Application` クラスを継承しているため、同じく `android.app.Application` クラスを継承したクラスを利用したい場合は、下記の方法で対応可能です。

ここでは、 `android.app.Application` クラスを継承した `MyApplication` クラスを利用する例を示します。

<details open>
<summary>Kotlin</summary>

```kotlin
// 1. SDK を利用するために、それぞれインポートします。
import jp.co.optim.optimalremote.IORIASessionProvider
import jp.co.optim.optimalremote.ORIASession
import jp.co.optim.optimalremote.ORIASessionProvider
import android.app.Application
import android.content.Context

// 2. IORIASessionProvider を実装します。
class MyApplication : Application(), IORIASessionProvider {
    // 3. ORIAApplication の実装部は ORIASessionProvider と同じため、これを利用します。
    private val mSessionProvider = ORIASessionProvider()

    ...

    override fun initSession(context: Context, profile: String, key: String) {
          // 4. オペレータとの接続準備を行います。
        mSessionProvider.initSession(context, profile, key)
    }

    // 5. ORIASession インスタンスを取得します。
    override val session: ORIASession?
        get() = mSessionProvider.session

    ...
}

```

</details>

<details>
<summary>Java</summary>

```java
// 1. SDK を利用するために、それぞれインポートします。
import jp.co.optim.optimalremote.IORIASessionProvider;
import jp.co.optim.optimalremote.ORIASession;
import jp.co.optim.optimalremote.ORIASessionProvider;
import android.app.Application;
import android.content.Context;
...

// 2. IORIASessionProvider を実装します。
public class MyApplication extends Application implements IORIASessionProvider {
  // 3. ORIAApplication の実装部は ORIASessionProvider と同じため、これを利用します。
  private final ORIASessionProvider mSessionProvider = new ORIASessionProvider();

  ...

  @Override
  public void initSession(Context context, String profile, String key) {
    // 4. オペレータとの接続準備を行います。
    mSessionProvider.initSession(context, profile, key);
  }

  @Override
  public ORIASession getSession() {
    // 5. ORIASession インスタンスを取得します。
    return mSessionProvider.getSession();
  }

  ...
}

```

</details>

## TextureView をキャプチャする

SDK は、既定値で `TextureView` のキャプチャを無効化しています。 `TextureView` のキャプチャを有効化するには、`ORIASession` オブジェクトの `setCaptureTextureViewEnabled` メソッドで引数に `true` を渡してください。
無効化するには、 `false` を渡してください。
`TextureView` のキャプチャを有効化すると、無効化した場合に比べてパフォーマンスに影響を及ぼす可能性がございます。
`setCaptureTextureViewEnabled` の設定値は、サポート中に変更できません。 `ORIASession` オブジェクトの `open` メソッドを呼び出す前に設定してください。

## SDK 表示 の言語切り替え

SDK が UI 上に表示している言語を切り替えるには以下のように`ORIASession`オブジェクトの`locale`プロパティを更新してください。

<details open>
<summary>Kotlin</summary>

```kotlin
// 英語に切り替える
session.locale = Locale.EN

// 日本語に切り替える
session.locale = Locale.JA

// 端末設定に従う
session.locale = Locale.SYSTEM
```

</details>

<details>
<summary>Java</summary>

```java
// 英語に切り替える
session.setLocale(Locale.EN);

// 日本語に切り替える
session.setLocale(Locale.JA);

// 端末設定に従う
session.setLocale(Locale.SYSTEM);
```

</details>
