# Changelog

このプロジェクトに対するすべての重要な変更は、このファイルに文書化されます。

## [2.0.3] - 2024-12-18

### Changed

- 音声通話機能を除いた SDK（[optimal_remote_no_voip.aar](./optimal_remote/optimal_remote_no_voip.aar)）を追加しました。

> [!NOTE]
> optimal_remote_no_voip.aar は音声通話機能に関するモジュールを除いていますが、その他の機能に差分はありません。

- SDK が UI 上に表示している言語を切り替える API として`ORIASession.locale`を追加しました。

> [!NOTE]
> SDK の表示言語切り替えの詳細は[CUSTOMIZE.md](./docs/CUSTOMIZE.md)をご参照ください。

### Fixed

- TextureView のキャプチャを有効にした場合に、TextureView が含まれていない ViewGroup のキャプチャができない不具合を修正しました。

## [2.0.2] - 2024-10-01

### Changed

- Android 15 に対応しました。

> [!NOTE]
> 16 KB ページサイズのデバイスでも動作するように変更しています。

## [2.0.1] - 2024-04-09

### Changed

- 音声通話機能を改善しました。

> [!NOTE]
> 音声通話機能で使用しているライブラリのバージョンを更新しています。

## [2.0.0] - 2023-11-30

### Changed

- OptimalRemote In-App Remote SDK for Android の開発言語を Java から Kotlin に移行しました。

> [!WARNING]
> 本 SDK の形態を jar から aar に変更しています。
