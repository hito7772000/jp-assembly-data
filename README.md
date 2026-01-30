# 議会オープンデータ (Assembly Open Data)

このディレクトリは DBSR を使う自治体の議会データを JSON 化した出力先です。

## ディレクトリ構成
```
<city-root>/
  metas/<term>.json
  members/<term>.json
  sessions/<year>/<session_slug>.json
  bills/<year>/<session_slug>.json
  scores/<year>/<session_slug>_activities.json
  scores/<year>/<session_slug>_attendance.json
  scores/<year>/<session_slug>_votes.json
  scores/<year>/<session_slug>_motions.json
```

## JSON 項目

### metas/<term>.json
- `assembly_name`: 議会名
- `assembly_type`: 議会種別 (`city` など)
- `term_start`: 任期開始日
- `term_end`: 任期終了日
- `seats`: 議席数
- `parties[]`: 会派一覧
  - `id`: 会派 ID
  - `name`: 会派名
  - `seats`: 所属議員数
- `sources`: 参照元
- `last_updated`: 最終更新日 (あれば)

### members/<term>.json
- `term.start`: 任期開始日
- `term.end`: 任期終了日
- `members[]`: 議員一覧
  - `id`: 議員 ID
  - `name`: 氏名
  - `party_id`: 会派 ID
  - `profile_url`: プロフィール URL
  - `party_history[]`: 会派履歴
    - `party_id`: 会派 ID
    - `from`: 開始日

### sessions/<year>/<session_slug>.json
- `session_id`: 会期 ID
- `name`: 会期名
- `type`: `regular` / `extraordinary`
- `year`: 開催年
- `start_date`: 開会日
- `end_date`: 閉会日
- `agenda[]`: 日程
  - `date`: 日付
  - `title`: 日程タイトル
- `bills[]`: 議案 ID 一覧
- `source_url`: 参照元

### bills/<year>/<session_slug>.json
- `session_id`: 会期 ID
- `source_url`: 参照元
- `bills[]`: 議案一覧
  - `bill_id`: 議案 ID
  - `title`: 議案名
  - `category`: 区分 (budget/ordinance/ordinance_amendment/petition/proposal/report)
  - `proposer`: 提案者 (`mayor` 固定)
  - `status`: 状態 (未取得なら null)
  - `related_session_id`: 会期 ID
  - `source_url[]`: 参照元 URL

### scores/<year>/<session_slug>_activities.json
- `session_id`: 会期 ID
- `source_url`: 参照元
- `session_metadata.start_date`: 開会日
- `session_metadata.end_date`: 閉会日
- `general_questions[]`: 一般質問
  - `question_id`: 質問 ID
  - `member_id`: 議員 ID
  - `question_date`: 質問日

### scores/<year>/<session_slug>_attendance.json
- `session_id`: 会期 ID
- `source_url`: 参照元
- `session_metadata.start_date`: 開会日
- `session_metadata.end_date`: 閉会日
- `session_metadata.total_members`: 議員数
- `session_metadata.total_session_days`: 会期日数
- `attendance`: 出席情報 (key は議員 ID)
  - `sessions_attended`: 出席回数
  - `total_sessions`: 会期日数
  - `attendance_rate`: 出席率

### scores/<year>/<session_slug>_votes.json
- `session_id`: 会期 ID
- `source_url`: 参照元
- `votes[]`: 採決一覧
  - `bill_id`: 議案 ID
  - `title`: 議案名
  - `factions`: 会派別賛否 (`yes/no/abstain/absent`)
  - `factions_detail`: 会派別人数 (あれば)
  - `result`: 議決結果
  - `decided_date`: 議決日

### scores/<year>/<session_slug>_motions.json
- `session_id`: 会期 ID
- `source_url`: 参照元
- `motions[]`: 動議一覧
  - `motion_id`: 動議 ID
  - `title`: 動議名
  - `factions`: 会派別賛否
  - `factions_detail`: 会派別人数 (あれば)
  - `result`: 議決結果
  - `decided_date`: 議決日

## データのポイント
- `members/<term>.json` の `id` が各 JSON の `member_id` と対応します。
- `scores/*_motions.json` は動議がある会期のみ生成されます。
- `source_url` は参照元の DBSR URL です。
