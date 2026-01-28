# 議会データモデル仕様（Hino Assembly Open Data）

このリポジトリは、日野市議会の議会活動を **JSON 形式で機械可読に記録する標準データモデル** です。

公開されている議事録やデータをそのまま集約・加工し、市民が簡単に議会情報を分析・再利用できるようにしています。

本ドキュメントでは、各ファイルの役割と構造を説明します。

---

# 📂 ファイル構成

```
hino-city/
├── metas/
│   └── 2024.json              ← 議会の基本情報・会派定義
├── members/
│   └── 2024.json              ← 議員マスターデータ
├── sessions/
│   └── 2024/
│       └── session_01.json    ← 会期の詳細情報
├── bills/
│   └── 2024/regular_01/
│       ├── batch_01.json      ← 議案データ（1-15件目）
│       ├── batch_02.json      ← 議案データ（16-30件目）
│       └── batch_03.json      ← 議案データ（31-48件目）
└── scores/2024/
    ├── regular_01_votes.json      ← 投票結果（会派別）
    ├── regular_01_attendance.json ← 出席情報（議員別）
    └── regular_01_activities.json ← 一般質問（議員別）
```

---

# 🧩 各ファイルの説明

## 1. metas/2024.json（議会メタデータ）

**役割：** 議会全体の基本情報と会派の定義を一元管理

| フィールド | 説明 | 例 |
|-----------|------|-----|
| `assembly_name` | 議会の正式名 | 日野市議会 |
| `assembly_type` | 議会の種別 | city（市議会） |
| `term_start` | 議員任期開始日 | 2022-03-08 |
| `term_end` | 議員任期終了日 | 2026-03-08 |
| `seats` | 総議席数 | 26 |
| `parties[]` | 会派情報（下記参照） | - |

### parties[] の構造

| フィールド | 説明 | 例 |
|-----------|------|-----|
| `id` | **会派ID**（scores.json で参照） | party_jcp, party_komei |
| `name` | 会派の公式名称 | 日本共産党日野市議団 |
| `seats` | その会派の議席数 | 4 |

**サンプル：**
```json
{
  "assembly_name": "日野市議会",
  "assembly_type": "city",
  "term_start": "2022-03-08",
  "term_end": "2026-03-08",
  "seats": 26,
  "parties": [
    { "id": "party_jcp", "name": "日本共産党日野市議団", "seats": 4 },
    { "id": "party_ldp", "name": "自由民主党日野市議団", "seats": 4 }
  ]
}
```

---

## 2. members/2024.json（議員マスターデータ）

**役割：** 議員の基本情報を管理（ID、名前、所属党派）

scores ファイルで議員を参照する場合は、`member_id` を使用します。

| フィールド | 説明 | 例 |
|-----------|------|-----|
| `id` | **議員ID**（scores.json で参照） | hino_001, hino_002 |
| `name` | 議員の氏名 | わたなべ三枝 |
| `party_id` | 所属会派ID | party_jcp |
| `party_history[]` | 党派変更履歴 | - |

**サンプル：**
```json
{
  "term": {"start": "2022-03-08", "end": "2026-03-08"},
  "members": [
    {
      "id": "hino_001",
      "name": "わたなべ三枝",
      "party_id": "party_jcp",
      "party_history": [
        {"party_id": "party_jcp", "from": "2022-03-08"}
      ]
    }
  ]
}
```

---

## 3. bills/2024/regular_01/batch_*.json（議案データ）

**役割：** 議会で審議される各議案の詳細情報

| フィールド | 説明 | 例 |
|-----------|------|-----|
| `bill_id` | **議案ID**（scores.json で参照） | 2024-001 |
| `title` | 議案の表題 | 令和6年度一般会計予算 |
| `category` | 議案の種別 | budget / ordinance_amendment / petition |
| `proposer` | 提案者 | mayor / assembly_members / citizens |
| `related_session_id` | 扱った会期ID | 2024-regular-01 |
| `source_url` | 公式ページへのリンク | https://... |

**サンプル：**
```json
{
  "batch_id": "2024-batch-01",
  "bills": [
    {
      "bill_id": "2024-001",
      "title": "令和5年度下水道事業会計補正予算（第2号）専決処分の承認",
      "category": "budget",
      "proposer": "mayor",
      "related_session_id": "2024-regular-01",
      "source_url": "https://..."
    }
  ]
}
```

---

## 4. scores/2024/regular_01_votes.json（投票結果）

**役割：** 各議案の採決結果と会派別の投票内容を記録

| フィールド | 説明 | 例 |
|-----------|------|-----|
| `session_id` | 会期ID | 2024-regular-01 |
| `votes[]` | 議案ごとの投票データ | - |

### votes[] の構造

| フィールド | 説明 | 例 |
|-----------|------|-----|
| `bill_id` | 議案ID | 2024-001 |
| `factions` | 会派別投票結果 | {"yes": ["party_jcp"], ...} |

### factions の構造

```json
"factions": {
  "yes": ["party_jcp", "party_komei"],      // 賛成会派
  "no": ["party_ldp"],                      // 反対会派
  "abstain": [],                            // 棄権会派
  "absent": ["party_independent"]           // 欠席会派
}
```

投票情報が公開されていない場合は `null`：

```json
"factions": null
```

**サンプル：**
```json
{
  "session_id": "2024-regular-01",
  "votes": [
    {
      "bill_id": "2024-001",
      "factions": {
        "yes": [],
        "no": [],
        "abstain": [],
        "absent": []
      }
    },
    {
      "bill_id": "2024-proposal-001",
      "factions": {
        "yes": ["party_jcp", "party_komei", "party_mirai", "party_network"],
        "no": ["party_ldp"],
        "abstain": [],
        "absent": ["party_independent"]
      }
    }
  ]
}
```

---

## 5. scores/2024/regular_01_attendance.json（出席情報）

**役割：** 議員の本会議出席状況と欠席理由を記録

| フィールド | 説明 | 例 |
|-----------|------|-----|
| `session_id` | 会期ID | 2024-regular-01 |
| `attendance` | 議員別の出席情報 | - |

### attendance の構造

議員IDをキーとした出席データ：

| フィールド | 説明 | 例 |
|-----------|------|-----|
| `sessions_attended` | 出席したセッション数 | 5 |
| `total_sessions` | 総セッション数 | 5 |
| `attendance_rate` | 出席率（%） | 100.0 |
| `absences[]` | 欠席記録（理由付き） | [{"session_id": "...", "reason": "病欠"}] |

**サンプル：**
```json
{
  "session_id": "2024-regular-01",
  "attendance": {
    "hino_001": {
      "sessions_attended": 5,
      "total_sessions": 5,
      "attendance_rate": 100.0,
      "absences": []
    },
    "hino_021": {
      "sessions_attended": 3,
      "total_sessions": 5,
      "attendance_rate": 60.0,
      "absences": [
        {"session_id": "2024-02-28", "reason": "病欠"},
        {"session_id": "2024-03-06", "reason": "公務出張"}
      ]
    }
  }
}
```

**欠席理由の例：**
- 病欠
- 公務出張
- その他

---

## 6. scores/2024/regular_01_activities.json（一般質問）

**役割：** 議員が本会議で行った一般質問を記録

| フィールド | 説明 | 例 |
|-----------|------|-----|
| `session_id` | 会期ID | 2024-regular-01 |
| `general_questions[]` | 質問記録の配列 | - |

### general_questions[] の構造

| フィールド | 説明 | 例 |
|-----------|------|-----|
| `question_id` | 質問ID | gq_001 |
| `member_id` | 質問した議員ID | hino_001 |
| `question_date` | 質問日 | 2024-02-21 |

**サンプル：**
```json
{
  "session_id": "2024-regular-01",
  "session_metadata": {
    "start_date": "2024-02-20",
    "end_date": "2024-03-22"
  },
  "general_questions": [
    {
      "question_id": "gq_001",
      "member_id": "hino_001",
      "question_date": "2024-02-21"
    },
    {
      "question_id": "gq_002",
      "member_id": "hino_007",
      "question_date": "2024-02-22"
    }
  ]
}
```

---

# 🔗 データの参照関係

```
metas.json（会派定義）
  ↓ (party_id を参照)
  
members.json（議員情報）
  ↓ (member_id を参照)

bills/2024/regular_01/*.json（議案情報）
  ↓ (bill_id を参照)

scores/2024/regular_01_*.json（採決・出席・質問）
  ・votes.json: bill_id で議案と連携、factions で会派を参照
  ・attendance.json: member_id で議員を参照
  ・activities.json: member_id で議員を参照、question_date で記録
```

---

# 📊 使用例

### 例1：議員 hino_001 の活動を取得

```javascript
// 1. members.json から議員情報を取得
const member = members.find(m => m.id === "hino_001");
console.log(`${member.name}（${member.party_id}）`);
// → "わたなべ三枝（party_jcp）"

// 2. attendance.json から出席状況を取得
const attendance = attendanceData.attendance["hino_001"];
console.log(`出席率：${attendance.attendance_rate}%`);

// 3. activities.json から一般質問を取得
const questions = activitiesData.general_questions.filter(
  q => q.member_id === "hino_001"
);
console.log(`質問数：${questions.length}`);
```

### 例2：議案ごとの投票パターンを分析

```javascript
// 1. bills から議案情報を取得
const bill = bills.find(b => b.bill_id === "2024-001");

// 2. votes.json から投票結果を取得
const vote = votesData.votes.find(v => v.bill_id === "2024-001");

// 3. metas.json を参照して会派名を解決
const factionNames = vote.factions.yes.map(partyId => {
  return metas.parties.find(p => p.id === partyId).name;
});
console.log(`${bill.title} に賛成: ${factionNames.join(", ")}`);
```

---

# ✨ このモデルの特徴

| 特徴 | メリット |
|------|--------|
| **JSON 形式** | テキストベースで Git による差分管理が容易 |
| **ID による参照** | 各ファイル間で ID を使用し、データの冗長化を避ける |
| **主観を排除** | 公開されているデータをそのまま記録、スコアリングやカテゴリ分けはしない |
| **柔軟な欠席理由** | 欠席の理由を記録し、単なる「欠席」以上の情報を提供 |
| **スケーラビリティ** | 複数自治体・複数会期への拡張が容易 |
| **再利用可能** | 市民向けアプリ、学術研究、可視化ツールなど自由に活用可能 |

---

# 📝 更新履歴

| 日付 | 内容 |
|------|------|
| 2026-01-28 | 最終スキーマを確定。members.json、スコアファイルの分割化を反映 |

