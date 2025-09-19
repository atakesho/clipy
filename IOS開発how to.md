## CursorでiOSアプリを開発しよう（ホットリロード対応）

このドキュメントは、元記事の本文要点だけを抽出し、実践手順がすぐ分かるように整理したものです。

---

## はじめに
- iOSアプリ開発では最終的なビルド/配布にXcodeが必須。
- Cursor（VS Code系エディタ）を併用すると、AI支援やエディタ機能で開発を効率化できる。

## Cursorとは
- VS CodeライクなAIエディタ。VS Codeユーザーは移行が容易。
- macOSのキーバインドは近いが、完全にXcode互換ではない。

## Cursorの便利機能
### Docs
- 任意のドキュメントを読み込ませ、チャットから質問できる。
- Editor右側のChatで  → ドキュメントを追加。

### Composer
- 複数ファイルにまたがるコード生成・編集支援。
- 例: 「MVVMで実装、ファイル分割」などの要件をまとめて指示可能。

---

## ビルドはどうする？
### Xcodeからビルド
- 当然可能。プロジェクトの管理方式により手順が少し変わる。

#### Folder管理とGroup管理
- 目安:
  - Xcode 16以降で新規作成: Folder管理（推奨）
  - Xcode 15以前の多く: Group管理
- Folder管理の利点: ファイル追加がそのままXcodeに反映されやすく、コンフリクトも減る。

#### Group管理の場合の注意
- Cursorで作成したファイルがXcodeに自動反映されないことがある。
- Xcodeで「Add Files to …」から、追加したファイルを手動で取り込む。

---

## Cursorからビルド（SweetPad拡張）
1) Cursorの拡張マーケットプレイスで「SweetPad」をインストール。
2) エディタ下部からビルドスキーム/シミュレータ機種を選択。
3) コマンドパレット（Cmd+Shift+P）→  を実行。
4) 失敗する場合は  のコマンドラインツール更新などを確認。

---

## ホットリロード（InjectionIII + Inject）
### 使うもの
- InjectionIII（macOSアプリ）
- Inject（SPM もしくは CocoaPods）

### 導入（どちらか一方でOK）
- SPM: 
- CocoaPods: 

### Build Settings
- Xcode → PROJECT → Build Settings → Other Linker Flags（Debug）に以下を追加:
  - 
  - 
- それぞれダブルクォーテーションで囲わないように注意。

### InjectionIII のセットアップ
1) InjectionIII.app をダウンロードし、 配下へ配置。
2) InjectionIII を起動。
3) メニューから「Open Project」で対象プロジェクトのフォルダを選択。
4) 再度メニューから「Prepare Project」を実行 → アラートで「Go ahead」。
5) 成功すると  で囲まれたコードがプロジェクトに差し込まれる。

### 動かし方
- XcodeでビルドするとInjectionが有効化され、Cursorでコードを保存（Cmd+S）するたびにシミュレータへ反映。
- ディレクトリ権限エラーが出る場合は、プロジェクトを別ディレクトリへ移動して再試行。

---

## オートコンプリート改善（任意）
- コード補完を強化したい場合:

- Cursorのコマンドパレットで  を実行。

---

## 使ってみた所感（抜粋）
- AI支援でコード検索やリファクタがスムーズ。
- SweetPadでCursorから直接ビルド可能。ホットリロードも組み合わせると開発速度が上がる。

---

## 参考
- SweetPad（Cursor拡張）
- InjectionIII / Inject（Hot Reload）
- Xcode 16 の Folder管理（Group管理→Folder管理への移行も可能）

