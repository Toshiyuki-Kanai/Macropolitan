# Macropolitan

**City-Building Simulation for macOS**

Macropolitan（マクロポリタン）は、macOS向けの都市建設・経営シミュレーションゲームです。SwiftUI / AppKitのネイティブUIと、オープンソースの **Micropolis C++ Core** を組み合わせています。WebView・Electron・WebAssemblyは使用しません。

Macropolitan is a native macOS city-building simulation built with SwiftUI / AppKit and the Micropolis C++ Core. This unofficial adaptation preserves the original engine and its credits.

Electronic Arts、元プロジェクト、Micropolis商標権者による公式リリースや推奨製品ではありません。

## 対応環境と開発状況

- **macOS 27.0以降 / Apple Silicon（arm64）**。Intel Macは対象外です。
- アプリのバージョン：**1.0 / Build 1**。
- 開発環境：macOS 27 SDKとSwift 6に対応するXcode。本工程の検証環境はXcode 27.2 / Swift 6.4.2です。
- 公開予定先：[Toshiyuki-Kanai/Macropolitan](https://github.com/Toshiyuki-Kanai/Macropolitan) の **GitHub Releasesのみ**。現在はPrivateで開発中で、Releaseやダウンロード用DMGはまだ提供していません。
- 独自Webサイト、Mac App Store、Steamでの公開は現時点では予定していません。
- 新しいアプリアイコンは未登録です。旧アイコンは含みません。配布用署名・公証・DMG作成は別工程です。

## 主な機能

- 都市候補・難易度の選択、新規都市作成、既存都市の読み込み・保存。
- 道路・鉄道・送電線、住宅／商業／工業地区、公園・発電所・公共施設などの建設、撤去、建設プレビューと連続／矩形操作。
- シミュレーション開始・停止・一時停止、4段階の速度、人口・資金・年月・需要の表示。
- 税率・サービス予算・手動年次予算、財政明細、都市評価、履歴グラフ、区域調査、通知と災害操作。
- 14種類のマップ表示、概観マップ、パン・ズーム、原版タイル、7種類の移動スプライトと効果音。
- 日本語／英語、Light／Darkモード、macOS標準ファイル操作と未保存確認。

ゲーム計算はC++エンジンが担います。MicropolisJ v2.0.0は機能仕様の参考であり、Java版と内部アルゴリズムの完全一致を保証するものではありません。予算計算は既存C++方式です。

## ビルドと起動

Xcode付属のCommand Line Toolsを使用します。外部Swift Package、Node.js、pnpmは不要です。リポジトリのルートで実行してください。

```sh
swift build --package-path packages/micropolis-engine --scratch-path build/swiftpm --arch arm64

xcodebuild -project apps/micropolis-macos/MicropolisMac.xcodeproj \
  -scheme MicropolisMac -configuration Debug \
  -destination 'platform=macOS,arch=arm64' -derivedDataPath build/xcode build

xcodebuild -project apps/micropolis-macos/MicropolisMac.xcodeproj \
  -scheme MicropolisMac -configuration Release \
  -destination 'platform=macOS,arch=arm64' -derivedDataPath build/xcode build
```

出力は `build/xcode/Build/Products/Debug/Macropolitan.app` と `build/xcode/Build/Products/Release/Macropolitan.app` です。Xcodeで `apps/micropolis-macos/MicropolisMac.xcodeproj` を開き、Scheme **MicropolisMac**、実行先 **My Mac** でもビルド・実行できます。

既存のビルド・起動スクリプトは `bash apps/micropolis-macos/script/build_and_run.sh` です。スクリプトの出力先は `build/micropolis-macos/` です。Target／Scheme／内部実行ファイル名はMicropolisMac、Swiftモジュール名はMicropolis_for_macOSを維持しています。

共有設定は個人Teamを含まないローカルad-hoc署名です。App Sandboxと既存のファイルアクセス権限を維持します。Releaseビルド成功は、配布用Developer ID署名・Apple公証の完了を意味しません。

## 回帰テスト

```sh
MACOSX_DEPLOYMENT_TARGET=27.0 bash apps/micropolis-macos/script/verify_graphics.sh
MACOSX_DEPLOYMENT_TARGET=27.0 bash apps/micropolis-macos/script/verify_sidebar.sh
MACOSX_DEPLOYMENT_TARGET=27.0 bash apps/micropolis-macos/script/verify_window_lifecycle.sh
MACOSX_DEPLOYMENT_TARGET=27.0 bash apps/micropolis-macos/script/verify_sandbox_save.sh
```

`verify_graphics.sh` はC++／C API・Swiftエンジン統合・素材・Canvas描画の検証を含みます。ウインドウ・Sandbox保存などはログイン済みmacOS GUIセッションを必要とします。Java版との互換性試験には別途固定版JavaソースとJDKが必要で、通常ビルドの必須依存ではありません。ビルド／検証出力は `build/` 以下でGit管理対象外です。

## 保存形式と制限

`.cty`形式、Bundle ID `org.micropolis.native.macos`、文書UTI `org.micropolis.city`を維持します。既存の保存・読込ロジック、UserDefaultsのキー、Sandboxコンテナ・bookmarkの識別子を変更しません。ただし名称変更だけで、署名や権限を含むすべての既存状態の継承を保証するものではありません。

1プロセスにつき編集可能な都市ウインドウは1つです。古典形式とC++拡張形式を扱いますが、拡張mapや一部タイルはJava版と互換ではありません。Undo、自動保存、BGM、シナリオ選択UI、地形編集UIは実装していません。保存仕様・操作・検証上の制限は[アプリの詳細README](apps/micropolis-macos/README.ja.md)を参照してください。名称変更後のFinder／Dock／メニューバー表示、手動ファイル操作、数時間のGUI連続稼働は実機確認事項です。

## ライセンスとクレジット

原作MicropolisはWill Wrightによる設計、Unix移植等はDon Hopkinsによります。エンジン・原版素材のCopyright (C) 1989–2007 Electronic Arts Inc.と既存の帰属表示を保持しています。Macropolitanは改変された非公式移植版であり、元の製品そのものではありません。

- [LICENSE](LICENSE)、[GPL version 3](gpl-3.0.txt)、[EA追加条件](MicropolisGPLLicenseNotice.md)、[Micropolis Public Name License](MicropolisPublicNameLicense.md)を原文で保持しています。コードはGPL version 3 or later、該当するEA追加条件の下で提供します。無保証です。
- 原エンジン：[SimHacker/MicropolisCore](https://github.com/SimHacker/MicropolisCore)。移行元：[Toshiyuki-Kanai/micropolis-native-macos](https://github.com/Toshiyuki-Kanai/micropolis-native-macos)、固定Commit `adb7f6be70e310d0750323700f6a67ccaa15e0ba`。
- 原版画像・音声：[素材出典・配布条件](apps/micropolis-macos/Resources/OriginalGraphics/ASSET-NOTICE.ja.md)。原XPM・WAV・変換PNG・固定Commitとハッシュ・変換器を保持しています。
- 機能仕様の参考：MicropolisJ v2.0.0、Commit `4cbac80d8d0052cbc9f4e71130dad212dbe2b492`。関連記録は[機能比較](documentation/notes/engine/native-macos-micropolisj-parity.ja.md)を参照してください。

Micropolis is a registered trademark of Micropolis Corporation (Micropolis GmbH) and is licensed here as a courtesy of the owner under the Micropolis Public Name License. <https://www.micropolis.com>

製品名Macropolitanと原エンジン名Micropolisは区別します。名称ライセンスはコードのGPLとは別であり、表示は個別の許諾取得や公開承認の主張ではありません。公開前のライセンス・配布条件の最終確認は別工程です。

バイナリ配布時は、**同じCommit**の対応ソースを同じGitHub Releaseから取得できる構成とし、Tag・完全なCommit ID・Version／Build・ビルド環境・SHA-256を記録する方針です。対応ソースにはSwiftアプリ、C++ Core／C API、設定、必要な素材原本、変換・ビルドスクリプト、ライセンス文書を含めます。署名用秘密鍵・認証情報は含めません。現時点ではReleaseや配布用Tag・DMGを作成していません。

## 開発・移行記録

[移行結果](documentation/migration/step2-results.ja.md)、[コピー元・先ハッシュ台帳](documentation/migration/step2-manifest.json)、[初期Commit前監査](documentation/migration/step3-audit.ja.md)、`documentation/notes/engine/` は履歴資料です。旧名・当時の未作成状態・旧公開先・過去の検証結果が残りますが、現在の製品仕様や今回の実機検証を示すものではありません。現在の名称・構成はこのREADMEとビルド設定を参照してください。
