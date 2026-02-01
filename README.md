# jp-assembly-data
**Japan Assembly Data Format (JAD Format)**  
正規化された自治体議会データの JSON 形式

jp-assembly-data は、**jp-assembly プロジェクト**の中核をなす  
「自治体議会データの正規化フォーマット（JAD Format）」を定義・格納するリポジトリです。

議員・会派・議案・採決・質問・委員会・会期・活動指標など、  
自治体ごとに形式が異なる議会データを **統一された JSON 形式** に整理します。

本フォーマットは以下の原則に基づいて設計されています：

- **冗長性ゼロ**：同じ情報を複数箇所に持たない
- **全国統一**：どの自治体でも同じ構造で扱える
- **透明性**：市民が理解しやすく、検証可能
- **拡張性**：自治体固有情報は optional として吸収
- **時系列整合性**：任期・所属・役職は from/to で管理
- **スケール性**：全国展開に耐えるディレクトリ構造

---

## 📂 ディレクトリ構造（Directory Structure）

```
spec/
│  municipality.json        # 自治体メタデータ（自治体名・タグ など）
│
├─schema/
│  └─v1/                    # JAD Schema v1（JSON Schema）
│      bills.schema.json
│      members.schema.json
│      parties.schema.json
│      questions.schema.json
│      scores.schema.json
│      tags.schema.json
│      votes.schema.json
│
├─tags/                     # 全国共通の分類体系（タグ）
│  bill_tags.json           # 議案・質問の分類（大項目・中項目・全国共通 small・対象者）
│  member_tags.json         # 議員の専門分野（議員専用タグ）
│  party_tags.json          # 会派の思想・スタンス
│
│  └─local/                 # 自治体固有 small タグ
│      hino.json            # 例：日野市の固有議題タグ
│
└─terms/                    # 任期ごとの実データ（スナップショット）
    └─{term}/               # 例：2022-2025
        bills/              # 議案データ
        committees/         # 委員会データ
        members/            # 議員データ（party_history を含む）
        parties/            # 会派データ（構成は members 側で管理）
        questions/          # 一般質問・代表質問
        scores/             # 議員ごとの活動指標（出席・質問・投票）
        sessions/           # 会期情報
        votes/              # 採決データ
```

---

## 🏷 タグ体系（Tags）

JAD Format のタグ体系は、用途ごとに明確に分離されています。  
冗長性を避け、全国展開に耐える構造です。

```
spec/tags/
  bill_tags.json        # 議案・質問の分類（大項目・中項目・全国共通 small・対象者）
  member_tags.json      # 議員の専門分野（議員専用タグ）
  party_tags.json       # 会派の思想・スタンス
  local/
    hino.json           # 自治体固有 small タグ
```

---

### 1. bill_tags.json（議案・質問の分類）

議案・質問・委員会議題に付与する全国共通の分類タグ。

- **age**（対象者：学生・子育て世代・高齢者…）
- **field**（大項目：子育て・教育・交通…）
- **medium**（中項目：保育園・道路整備・ICT教育…）
- **small**（全国共通の小分類）

自治体固有 small は `spec/tags/local/` に格納。

---

### 2. member_tags.json（議員の専門分野）

議員の専門性を示すタグ。  
議案分類（field_xxx）とは別軸で、冗長性を避けるため独立。

例：
- member_field_childcare
- member_field_education

---

### 3. party_tags.json（会派の思想）

会派の政治的スタンスを示す全国共通タグ。

例：
- ideology_civic
- ideology_conservative

---

### 4. local/（自治体固有 small）

自治体ごとの固有議題（駅前整備・公園再整備など）は  
`spec/tags/local/{municipality}.json` に格納。

---

## 🧑‍💼 members（議員）

議員の基本情報と、  
**会派所属（party_history）＋役職（roles）** を一元管理します。

```json
{
  "id": "m001",
  "official_number": "001",
  "name": "山田太郎",
  "joined": "2022-04-01",
  "resigned": null,
  "profile_url": "https://...",
  "party_history": [
    {
      "party_id": "p001",
      "from": "2022-04-01",
      "to": null,
      "roles": [
        { "role": "leader", "from": "2023-04-01", "to": "2024-03-31" }
      ]
    }
  ],
  "tags": ["member_field_childcare"],
  "source": [...]
}
```

---

## 🏛 parties（会派）

会派の属性のみを保持します。  
所属議員は member 側で管理します。

```json
{
  "id": "p001",
  "name": "市民クラブ",
  "tags": ["ideology_civic"],
  "source": [...]
}
```

---

## 📑 bills（議案）

議案の基本情報とタグ分類。

```json
{
  "id": "b001",
  "title": "令和5年度一般会計予算",
  "tags": ["field_tax", "tax_resident"],
  "source": [...]
}
```

---

## 🗳 votes（採決）

議員ごとの投票、会派ごとの投票、結果、票数を記録します。

```json
{
  "id": "v001",
  "bill_id": "b001",
  "session_id": "s001",
  "date": "2023-03-15",
  "result": "passed",
  "counts": { "yes": 1, "no": 1, "abstain": 0, "absent": 0 },
  "votes": {
    "members": [
      { "member_id": "m001", "vote": "yes" }
    ],
    "parties": [
      {
        "party_id": "p002",
        "vote": "split",
        "details": {
          "yes_count": 2,
          "no_count": 1,
          "abstain_count": 0,
          "absent_count": 0,
          "members": null
        }
      }
    ]
  },
  "roles": [
    { "member_id": "m010", "role": "chair" }
  ]
}
```

---

## ❓ questions（質問）

一般質問・代表質問など。

```json
{
  "id": "q001",
  "member_id": "m001",
  "session_id": "s001",
  "type": "general",
  "title": "子育て支援について",
  "tags": ["childcare_support"],
  "source": [...]
}
```

---

## 🧮 scores（議員ごとの活動指標）

任期ごとに集計した出席・質問・投票のサマリー。

```json
{
  "member_id": "m001",
  "attendance": {
    "regular_sessions": { "attended": 12, "absent": 1 },
    "special_sessions": { "attended": 3, "absent": 0 },
    "committees": { "attended": 24, "absent": 2 }
  },
  "questions": { "general": 5, "representative": 1 },
  "votes": { "yes": 32, "no": 4, "abstain": 1, "absent": 3 },
  "source": [...]
}
```

---

## 📐 設計思想

- **冗長性ゼロ**
- **関係性は member 側に集約**
- **自治体差は optional で吸収**
- **全国展開に耐えるスケール性**

---

## 📄 ライセンス

Apache License Version 2.0

