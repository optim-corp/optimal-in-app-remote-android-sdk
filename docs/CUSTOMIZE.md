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

## SDK 表示 UI のデザインカスタマイズ

SDK が表示する UI の画像や文言はカスタマイズすることができます。

### 画像のカスタマイズ

指定のファイル名でプロジェクトに画像を追加することでカスタマイズすることができます。

例として、サポート中のアイコンを変更したい場合は`res/drawable-hdpi/optimal_remote_menu.png`というファイル名で、カスタマイズ後の画像をプロジェクトに追加してください。

各種ピクセル密度に応じてサブディレクトリ（`drawable-hdpi`の部分）は変更してください。詳細は以下をご参照ください。

- <https://developer.android.com/training/multiscreen/screendensities>

カスタマイズできる画像と対応するファイル名は以下のとおりです。

| 項番 | 画像                                | 備考                                         | デフォルト                                                        | 推奨サイズ（hdpi での倍率） |
| ---- | ----------------------------------- | -------------------------------------------- | ----------------------------------------------------------------- | --------------------------- |
| 1    | `optimal_remote_menu.png`           | 画面共有中のアイコン                         | <img src="./img/optimal_remote_menu.png" height="32px">           | 114 px × 114 px             |
| 2    | `optimal_remote_logo.png`           | 画面上部に表示されるロゴ画像                 | <img src="./img/optimal_remote_logo.png" height="32px">           | 206 px × 32 px              |
| 3    | `optimal_remote_ticket_bg.9.png`    | 受付番号の背景画像                           | <img src="./img/optimal_remote_ticket_bg.9.png" height="32px">    | 114 px × 90 px              |
| 4    | `optimal_remote_speaker_loud.png`   | 画面共有中メニューのスピーカーオン時のボタン | <img src="./img/optimal_remote_speaker_loud.png" height="32px">   | 144 px × 144 px             |
| 5    | `optimal_remote_speaker_normal.png` | 画面共有中メニューのスピーカーオフ時のボタン | <img src="./img/optimal_remote_speaker_normal.png" height="32px"> | 144 px × 144 px             |
| 6    | `optimal_remote_mic_on.png`         | 画面共有中メニューのマイクオン時のボタン     | <img src="./img/optimal_remote_mic_on.png" height="32px">         | 144 px × 144 px             |
| 7    | `optimal_remote_mic_off.png`        | 画面共有中メニューのマイクオフ時のボタン     | <img src="./img/optimal_remote_mic_off.png" height="32px">        | 144 px × 144 px             |
| 8    | `optimal_remote_disconnect.png`     | 画面共有中メニューの切断ボタン               | <img src="./img/optimal_remote_disconnect.png" height="32px">     | 144 px × 144 px             |

> [!WARNING]
> 画像は png のみ対応しております。

> [!WARNING]
> 推奨サイズを大きく超える、または大きく下回る場合は UI がレイアウト崩れする場合がございます。

### 文言のカスタマイズ

指定のキーでプロジェクトに文字列リソースを追加することでカスタマイズすることができます。

例として、 画面共有中メニューの切断ボタンの英語版のラベルを`DISCONNECT`に変更する場合を以下に示します。

```xml:res/values/strings.xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="menu_disconnect_button_label">DISCONNECT</string>
</resources>
```

カスタマイズできる文言と対応するキーは以下のとおりです。

| 項番 | キー                           | 備考                                         | デフォルト（日本語）                               | デフォルト（英語）                                |
| ---- | ------------------------------ | -------------------------------------------- | -------------------------------------------------- | ------------------------------------------------- |
| 1    | `sdp_did_reserve_indicator`    | 受付番号の上に表示される文言                 | `下記の受付番号を\nオペレーターにお伝えください。` | `Please tell below \nReceipt Number to operator.` |
| 2    | `lobby_button_cancel`          | 受付番号の下に表示されるボタンのラベル       | `キャンセル`                                       | `Cancel`                                          |
| 3    | `menu_speaker_button_label`    | 画面共有中メニューのスピーカーボタンのラベル | `スピーカー`                                       | `Speaker`                                         |
| 4    | `menu_mute_button_label`       | 画面共有中メニューのマイクボタンのラベル     | `消音`                                             | `Mute`                                            |
| 5    | `menu_disconnect_button_label` | 画面共有中メニューの切断ボタンのラベル       | `切断`                                             | `Disconnect`                                      |

> [!WARNING]
> デフォルトの文言の文字数を大きく超える、または大きく下回る場合は UI がレイアウト崩れする場合がございます。
