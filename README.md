# Macropolitan — migration workspace

独立リポジトリへの移行ステップ2の作業フォルダです。Git履歴・GitHubリポジトリはまだ作成していません。
アプリの表示名はこの段階では **Micropolis for macOS** のままです。新名称・新アイコンの登録と公開用文書の整備は別工程です。旧アイコンは含みません。

## Build and validation

Apple Silicon / Xcode / macOS 27以降。外部Swift PackageやNode.jsは不要です。

```sh
swift build --package-path packages/micropolis-engine --scratch-path build/swiftpm --arch arm64
xcodebuild -project apps/micropolis-macos/MicropolisMac.xcodeproj -scheme MicropolisMac -configuration Debug -destination 'platform=macOS,arch=arm64' -derivedDataPath build/xcode build
bash apps/micropolis-macos/script/verify_graphics.sh
bash apps/micropolis-macos/script/verify_sidebar.sh
bash apps/micropolis-macos/script/verify_window_lifecycle.sh
bash apps/micropolis-macos/script/verify_sandbox_save.sh
```

Releaseは上記の `-configuration Release` で検証できます。共有設定は個人Teamを含まないローカルad-hoc署名です。Developer ID署名・公証は未実施です。
Java互換性テストは別途固定版JavaソースとJDKを必要とし、通常ビルドの必須依存ではありません。

## Provenance and licensing

移行元: https://github.com/Toshiyuki-Kanai/micropolis-native-macos

移行元Commit: `adb7f6be70e310d0750323700f6a67ccaa15e0ba`。上流Micropolisと画像・音声の出典は、保持したソースヘッダーおよびResources内のprovenance/ASSET-NOTICEを参照してください。
GPL version 3 or laterとEA追加条件、名称ライセンスは、それぞれ [LICENSE](LICENSE)、[GPL全文](gpl-3.0.txt)、[EA追加条件](MicropolisGPLLicenseNotice.md)、[名称ライセンス](MicropolisPublicNameLicense.md) を保持しています。
名称ライセンスとコード・素材のライセンスは別です。移行は公開可否の最終承認を意味しません。

既存アプリの説明: [README.ja.md](apps/micropolis-macos/README.ja.md)（旧名・旧公開先などの記載を含み、今後更新予定）。
移行監査: [step2-manifest.json](documentation/migration/step2-manifest.json)、[step2-results.ja.md](documentation/migration/step2-results.ja.md)。
