## 保存イベント可視化プラン

最終更新: 2025-01-30

---

### 1. 目的
- Capture UXのKPI（操作回数 ≤ 2.5、保存完了P95 ≤ 4秒、重複率など）を定量的に把握する。
- リリース後に保存導線の改善効果や失敗率を継続的にモニタリングする。

---

### 2. 収集済みデータ整理
保存処理ごとに `events` テーブルへ以下のJSONを記録済み：
- `type`: `capture`
- `meta_json.status`: `success | duplicate | error`
- `meta_json.duration_ms`: 保存完了までの処理時間（ms）
- `meta_json.saved_at`: アイテムの保存日時
- `meta_json.error_message`: 失敗時のメッセージ（Optional）

今後合わせて記録したい項目（必要なら拡張）：
- `meta_json.mode`: `local | cloud`
- `meta_json.trigger`: `dialog | autosave | clipper` など保存導線の識別
- `meta_json.retry`: 再試行回数

---

### 3. サマリ指標例
| 指標 | SQLサンプル | 備考 |
| --- | --- | --- |
| 保存成功率 | ```sql
select date_trunc('day', created_at) as day,
       sum((meta_json->>'status') = 'success')::int as success,
       count(*) as total,
       sum((meta_json->>'status') = 'success')::float / count(*) as success_rate
from public.events
where type = 'capture'
group by 1
order by 1 desc;
``` | mode別にフィルタするとローカル/クラウドの差を把握可能 |
| P95 保存時間 | ```sql
select date_trunc('day', created_at) as day,
       percentile_cont(0.95) within group (order by (meta_json->>'duration_ms')::numeric) as p95_ms
from public.events
where type = 'capture'
and meta_json->>'status' = 'success'
group by 1
order by 1 desc;
``` | 目標値 4秒以下 |
| 重複発生率 | ```sql
select date_trunc('week', created_at) as week,
       sum((meta_json->>'status') = 'duplicate')::int as duplicates,
       count(*) as total,
       round(sum((meta_json->>'status') = 'duplicate')::numeric / count(*) * 100, 2) as duplicate_pct
from public.events
where type = 'capture'
group by 1
order by 1 desc;
``` | UI改善が重複率に与える影響を測定 |
| エラー詳細 | ```sql
select meta_json->>'error_message' as error,
       count(*)
from public.events
where type = 'capture'
and meta_json->>'status' = 'error'
group by 1
order by count(*) desc;
``` | 失敗理由のトップ原因把握 |

---

### 4. ダッシュボード構成案
1. **トレンド**：成功率・重複率・P95保存時間の推移（折れ線）。
2. **時間帯別ヒートマップ**：時間帯×曜日の成功率。
3. **ステータス内訳**：成功/重複/失敗のドーナツチャート。
4. **エラーTop N**：失敗理由ランキング。

実装手段候補：
- Supabase SQL Editor + Saved Query（CSV出力で簡易レポート）
- Metabase/Redash などSaaS BIツールを接続
- 将来的にアプリ内でプレミアム向けに簡易ダッシュボードを提供

---

### 5. 運用フロー
1. 週次でダッシュボードを確認し、成功率とP95をKPIと照合。
2. 目標値を下回った場合、`events` をstatus/trigger/modeで深掘り。
3. 改善施策実施後は同指標をトレンドで比較して効果測定。
4. 90日以上前の詳細ログは集計値のみ保持するなど、コストとリテンション方針を検討。

---

### 6. 次のアクション
- `meta_json` へ `mode` や `trigger` を追記し、導線別に分析可能な形へ拡張。
- 上記SQLをSupabaseのSaved QueryまたはBIツールへ登録し、再利用可能なビューを整備。
- 保存イベントの集計をエンジニア以外でも確認できるよう、ドキュメントとダッシュボードを共有。
