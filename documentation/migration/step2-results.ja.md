# Macropolitan 移行ステップ2 — 結果

実施日: 2026-11-06。対象はローカル移植・参照修正・検証のみ。Git履歴作成、GitHub操作、名称変更、Developer ID署名、公証、DMG作成は実施していない。

## 移行対象

| 区分 | コピー数 |
|---|---:|
| SwiftUIアプリ・リソース・Xcode・検証スクリプト | 177 |
| C++ Core・C API・Swift Package・nativeテスト | 46 |
| 指定テスト都市 | 9 |
| エンジン開発記録 | 7 |
| ルートライセンス | 4 |
| 合計 | 243 |

コピー元合計2,673,563 bytes（約2.55 MiB）。新側での最小修正後、コピー対象243ファイルは2,672,664 bytes。追加文書・監査台帳・新規.gitignoreは別計数。ビルド／テスト出力は新側のbuild/以下に生成し、.gitignoreで除外した。元の成果物はコピーしていない。

全コピー対象の元・先SHA-256と変更理由は `step2-manifest.json`、コピーしなかった追跡パス5,116件は `step2-excluded-paths.txt` を参照。後者は除外ファイルの名前だけの監査記録であり、素材本体ではない。

## 新側だけの変更

1. `apps/micropolis-macos/MicropolisMac.xcodeproj/project.pbxproj`: 既存HEADの署名設定を採用。旧Forkの未コミット3行（Team ID2行・Release Apple Development指定1行）を取り込まなかった。Micropolis.iconの参照4行とApp Icon名指定2行を解除。共有設定はCODE_SIGN_IDENTITY="-"、CODE_SIGN_STYLE=Automatic、DEVELOPMENT_TEAM未指定。
2. `packages/micropolis-engine/Package.swift`: 移植しないWeb／モノレポファイルへのexclude記載を整理。現在存在するtests/examples/native.mk/README.native.mdのみをexclude。27ネイティブC++ソース、公開ヘッダー、NativeSmoke、ターゲット名は維持。
3. 新規README、.gitignore、本監査文書・ハッシュ台帳を追加。元のREADME／開発記録7件は履歴資料として維持したため、旧名・旧リポジトリURL・当時のWeb検証結果などが残る。現在の移行先でWebを検証したという意味ではない。

旧アイコン2ファイルは非移植。新規アイコン・代替画像は追加していない。実際のDebug/Release .appにCFBundleIconName/CFBundleIconFileの指定はない。

## 維持した識別子・設定

- 表示名／製品名: Micropolis for macOS（Macropolitanへの変更は別工程）
- Target／Scheme／実行ファイル: MicropolisMac
- Bundle ID: org.micropolis.native.macos
- 文書UTI: org.micropolis.city、拡張子.cty
- Version 1.0 / Build 1、最低macOS 27.0、arm64
- App Sandbox、user-selected read/write、security-scoped bookmark設定を保持。ソースEntitlementsはバイト一致。
- Hardened Runtimeのプロジェクト設定はYESを保持。ただし今回の成果物はローカルad-hoc署名でTeamIdentifierなし。配布用署名・公証を完了したものではない。DebugはXcodeがad-hoc署名時のHardened Runtime無効化を通知した。Releaseにも開発用get-task-allowが含まれるため、そのまま公開用としない。
- Swift/AppKit/Canvas、C++エンジン、C API、保存ロジック、ゲーム素材はバイト一致。ゲームUIへの意図的な変更はない。

## ビルド・自動検証

環境: macOS 27.2 (26B123)、Apple Silicon arm64、Xcode 27.2 (27B52)、Swift 6.4.2、MacOSX27.2 SDK。

| 項目 | 結果 |
|---|---|
| Swift Package arm64 build | 成功。外部Package依存なし |
| Xcode arm64 Debug / Release | 両方成功、相対パスの新側Packageをリンク |
| C++ Core / C API native.mk 5 suite | 全成功 |
| Swift EngineIntegration | 成功。全15建設ツール、プレビュー、保存再作成読込、座標、予算等 |
| 原画像／音声の検証 | 成功。960タイル、68原本、57スプライトPNG、11 WAV decode |
| GraphicsIntegration / Canvas | 成功。全65,536 raw値、キャッシュ、アニメ更新、1x/2x、pan/zoom/resize、保存都市とhaight、200回更新 |
| SidebarLayout | 成功。幅上限、縮小、項目境界、Overview分割、都市不変 |
| WindowLifecycle | 成功。閉じる保留・再表示・未保存都市保持等を独立テストウインドウで確認 |
| SandboxSaveProbe | 成功。新規保存・上書き・再読込・状態復元。使い捨てfixtureのみ使用 |
| ASan / UBSan native.mk 5 suite | 全成功、診断なし |
| NativeSmoke 新規都市 | 成功。cityTime 0→125、資金19,990、12,000タイル |
| NativeSmoke haight | 成功。cityTime 4,839→4,964、12,000タイル |
| 長時間・繰返し | 328,000ticks、20回の生成/haight読込、8シナリオ各1,100ticks等、既存テスト成功 |
| バンドル | 両構成の全135リソースとGPL/EA/名称ライセンス3文書のバイト一致、arm64、codesign verify成功 |
| ハッシュ／独立性 | 全コピー対象一致（明示した2修正を除く）、旧Forkの追跡ファイル5,359件とGit状態が開始時と一致 |

ログは `build/migration-validation/` に保存。初回nativeテストは新SDK既定のmacOS 27.2とSwift側27.0のリンク警告が出たため、`MACOSX_DEPLOYMENT_TARGET=27.0 make -B ... test` で新側ライブラリを作り直し、同じ環境変数で描画・サイドバー・ウインドウ・Sandbox保存を再実行して成功した。ソースや検証スクリプトは変更していない。XcodeのAppIntents metadata extraction skipped警告はAppIntents非使用による通知で、ビルド成功。NativeSmokeは現SwiftPMの実出力 `build/swiftpm/out/Products/Debug/NativeSmoke` を使用した。

## 除外・独立性・ライセンス

Screen Angel、content/vitamooとSims試作、Webアプリ、Web専用ソース／verify_wasm.py、Node/pnpm依存、旧GitHub Actions、旧.git、個人設定、旧成果物・キャッシュ、鍵／証明書／認証情報、旧アイコンは許可リスト外として非移植。ビルド外の想定外ファイル・シンボリックリンクなし。旧Forkへの絶対パス参照、実行時の外部ライブラリ参照なし。システムSDK／標準ライブラリと新側Packageのみ使用。

LICENSE、gpl-3.0.txt、MicropolisGPLLicenseNotice.md、MicropolisPublicNameLicense.mdは原文バイト一致。元の著作権・改変表示・素材のprovenanceと原本・変換器を保持。画像／音声は既存の固定Commit c98f6b08519887b450d9be198bfca5237aab6d0c由来の許諾根拠とハッシュを保持・検証した。指定9都市はテスト用原本をバイト一致で移植した。開発記録7件には除外素材の説明があるが、その素材自体は含まない。機密パターンの検査で秘密鍵・GitHubトークン・個人Team ID・旧Fork絶対パスの一致なし。この移植検査は新たな全面的法務監査・名称使用の承認ではない。

## 未実施・次工程

- 新アプリ本体を人が操作する最終GUI確認、音の聴感、保存パネルの手動操作、数時間のGUI連続稼働は未実施。オフスクリーンCanvasと専用AppKit/Sandboxテストは上記の自動検証として区別する。
- Java版との双方向互換性テストは未実施。JDK 25は存在するが、今回独立環境に固定版Javaソースを準備していない。専用スクリプト・固定ハッシュ・Javaテストは保持し、通常ビルド依存にしていない。
- Web/WASM回帰は対象外。Web専用コード／テストは移植していない。旧Forkは変更していない。
- 新名称と新アイコンの登録、公開先URLや公開用README/Aboutの更新、Git初期化、GitHub作成、配布用署名・公証・DMG・Releaseは未実施。

判定: ステップ2の独立ビルド・既存回帰テストは完了。ユーザー確認後に次工程へ進行可能。今回はここで停止する。
