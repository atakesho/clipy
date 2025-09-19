# AGENTS.md（Re:Clip Codex Brief）

## 目的
- URLを保存→AIがタイトル/要約/タグを付与→横断検索できるiOS中心のブックマークアプリ
- 直近はiOSローンチに必要な改修/安定化を最優先

## スタック / 架構
- Frontend: Vite + React + TypeScript + shadcn-ui + Tailwind
- iOS: Capacitor（本番ビルド資産を同梱、HashRouter）
- Backend: Supabase（Auth/DB/Edge Functions: `unfurl-metadata`, `enrich-content`, `apply-folder-rules`）

## ディレクトリ（抜粋）
- `src/pages/Inbox.tsx`（受信箱）、`src/pages/ItemDetail.tsx`（詳細）、`src/components/SaveItemDialog.tsx`（保存）
- `reclip/`（要件定義、ローンチ計画、QA、手順書、ASO など）

## 現状の既知方針
- 起動直後は`/inbox`へ。iOSは`HashRouter`
- セーフエリアは`viewport-fit=cover`＋CSSの`env/constant`で対策
- 保存後はローカル/クラウドでタイトル等を即時反映

## タスク運用ルール
- 変更は小粒化（≤ 1画面/1機能の単位）
- 影響範囲のファイルをコンテキストに添付
- 仕様は`reclip/`を正とし、差分は都度反映

## DoD（Definition of Done）
- 主要ケースの手動テストOK（`reclip/QAテストケース.md`）
- セーフエリア/白画面なし/保存→反映が確認できる
- `reclip/`文書を最新化

## 優先度（直近）
1. 保存UXの微調整（トースト/失敗時ガイド）
2. 検索UIの軽微な最適化（クラウド前提）
3. iOSアーカイブ/提出動線の整備（手順書通り）
