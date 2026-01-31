# 議会オープンデータ (Assembly Open Data)

このディレクトリは DBSR を使う自治体の議会データを JSON 化した出力先です。

## ディレクトリ構成
```
spec/terms/<term>/
  members/index.json
  members/<member_id>.json
  members/attachments/<member_id>.json
  parties/parties.json
  parties/membership/<member_id>.json
  committees/committees.json
  committees/membership/<member_id>.json
  committees/attachments/<committee_id>.json
  sessions/sessions.json
  sessions/attachments/<session_id>.json
  bills/<bill_id>.json
  bills/attachments/<bill_id>.json
  bills/summary/<bill_id>.json
  bills/classification/<bill_id>.json
  questions/<member_id>.json
  questions/attachments/<member_id>.json
  scores/<member_id>.json
  scores/attendance/<member_id>.json
  votes/<bill_id>.json
  votes/attachments/<bill_id>.json
```

## JSON 項目

### members/index.json
- `members[]`: 議員一覧
  - `id`: 議員 ID
  - `name`: 氏名
  - `official_number`: 議員番号
  - `joined`: 就任日
  - `resigned`: 退任日 (在任中は null)
  - `party_id`: 会派 ID

### members/<member_id>.json
- `id`: 議員 ID
- `name`: 氏名
- `official_number`: 議員番号
- `joined`: 就任日
- `resigned`: 退任日 (在任中は null)
- `profile_url`: プロフィール URL
- `party_history[]`: 会派履歴
  - `id`: 会派 ID
  - `from`: 開始日
  - `to`: 終了日 (在任中は null)

### members/attachments/<member_id>.json
- `member_id`: 議員 ID
- `attachments[]`: 添付資料
  - `id`: 添付 ID
  - `title`: タイトル
  - `url`: URL
  - `type`: 種別 (pdf など)
  - `pages`: ページ数
  - `source`: 出典
  - `notes`: 補足

### parties/parties.json
- `parties[]`: 会派一覧
  - `id`: 会派 ID
  - `name`: 会派名
  - `notes`: 補足

### parties/membership/<member_id>.json
- `member_id`: 議員 ID
- `history[]`: 会派所属履歴
  - `id`: 会派 ID
  - `role`: 役割
  - `from`: 開始日
  - `to`: 終了日 (在任中は null)

### committees/committees.json
- `committees[]`: 委員会一覧
  - `id`: 委員会 ID
  - `name`: 委員会名
  - `type`: 種別 (standing など)

### committees/membership/<member_id>.json
- `member_id`: 議員 ID
- `history[]`: 委員会所属履歴
  - `id`: 委員会 ID
  - `role`: 役割
  - `from`: 開始日
  - `to`: 終了日 (在任中は null)

### committees/attachments/<committee_id>.json
- `committee_id`: 委員会 ID
- `attachments[]`: 添付資料
  - `id`: 添付 ID
  - `title`: タイトル
  - `url`: URL
  - `type`: 種別
  - `pages`: ページ数
  - `source`: 出典
  - `notes`: 補足

### sessions/sessions.json
- `sessions[]`: 会期一覧
  - `id`: 会期 ID
  - `name`: 会期名
  - `start`: 開会日
  - `end`: 閉会日
  - `type`: `regular` / `extraordinary`
  - `notes`: 補足

### sessions/attachments/<session_id>.json
- `session_id`: 会期 ID
- `attachments[]`: 添付資料
  - `id`: 添付 ID
  - `title`: タイトル
  - `url`: URL
  - `type`: 種別
  - `pages`: ページ数
  - `source`: 出典
  - `notes`: 補足

### bills/<bill_id>.json
- `id`: 議案 ID
- `title`: 議案名
- `type`: 区分 (ordinance など)
- `proposer`: 提案者 (`mayor` など)
- `session_id`: 会期 ID
- `dates.submitted`: 提出日
- `dates.decided`: 議決日
- `documents.original_url`: 原文 URL
- `documents.attachments[]`: 添付資料 (あれば)

### bills/attachments/<bill_id>.json
- `bill_id`: 議案 ID
- `attachments[]`: 添付資料
  - `id`: 添付 ID
  - `title`: タイトル
  - `url`: URL
  - `type`: 種別
  - `pages`: ページ数
  - `notes`: 補足

### bills/summary/<bill_id>.json
- `bill_id`: 議案 ID
- `three_lines[]`: 3行要約
- `full`: 詳細要約

### bills/classification/<bill_id>.json
- `bill_id`: 議案 ID
- `categories[]`: 分類
- `tags[]`: タグ
- `policy_area`: 政策分野
- `importance`: 重要度
- `notes`: 補足

### questions/<member_id>.json
- `member_id`: 議員 ID
- `items[]`: 質問一覧
  - `id`: 質問 ID
  - `date`: 質問日
  - `theme`: テーマ
  - `summary`: 要約

### questions/attachments/<member_id>.json
- `member_id`: 議員 ID
- `attachments[]`: 添付資料
  - `id`: 添付 ID
  - `title`: タイトル
  - `url`: URL
  - `type`: 種別
  - `pages`: ページ数
  - `source`: 出典
  - `notes`: 補足

### scores/<member_id>.json
- `member_id`: 議員 ID
- `activity`: 活動スコア
- `attendance`: 出席スコア
- `questions`: 質問数
- `vote_participation`: 採決参加率

### scores/attendance/<member_id>.json
- `member_id`: 議員 ID
- `rate`: 出席率
- `records[]`: 出欠記録
  - `date`: 日付
  - `status`: `present` / `absent`

### votes/<bill_id>.json
- `bill_id`: 議案 ID
- `session_id`: 会期 ID
- `date`: 議決日
- `result`: 議決結果
- `counts.yes`: 賛成数
- `counts.no`: 反対数
- `counts.abstain`: 棄権数
- `counts.absent`: 欠席数
- `votes[]`: 議員別投票
  - `member_id`: 議員 ID
  - `vote`: `yes` / `no` / `abstain` / `absent`

### votes/attachments/<bill_id>.json
- `bill_id`: 議案 ID
- `attachments[]`: 添付資料
  - `id`: 添付 ID
  - `title`: タイトル
  - `url`: URL
  - `type`: 種別
  - `pages`: ページ数
  - `source`: 出典
  - `notes`: 補足

## データのポイント
- `members/index.json` の `id` が各 JSON の `member_id` と対応します。
- `committees/attachments/<committee_id>.json` は委員会 ID と一致します。
- `sessions/attachments/<session_id>.json` は会期 ID と一致します。
- `bills/*/<bill_id>.json` は議案 ID と一致します。
