# Japan Civic Data Standard (JCDS)

日本の自治体議会データを、全国共通の形式で整理・公開するためのデータ標準です。  
議員・会派・議案・採決・質問・委員会・出席・タグ分類など、  
自治体ごとにバラバラな情報を **ミニマルで統一的な JSON 形式** に整理します。

本標準は以下の原則に基づいて設計されています：

- **ミニマル**：必要最小限の項目のみを定義し、冗長性を排除する
- **全国統一**：自治体差を吸収し、どこでも同じ構造で扱える
- **透明性**：市民が理解しやすく、検証可能な構造
- **拡張性**：自治体固有の情報は optional として柔軟に追加可能
- **時系列整合性**：任期・所属・役職などは from/to で管理

---

## 📂 ディレクトリ構成

```
spec/
  municipalitys.json
  terms/
    {term}/
      members/
      parties/
      committees/
      bills/
      votes/
      questions/
      scores/
  tags/
    member_tags.json
    party_tags.json
    bill_tags.json
    local/
      hino.json
```

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
  "resigned": "2024-06-30",
  "profile_url": "https://...",
  "party_history": [
    {
      "party_id": "p001",
      "from": "2022-04-01",
      "to": "2024-06-30",
      "roles": [
        { "role": "leader", "from": "2023-04-01", "to": "2024-03-31" }
      ]
    }
  ],
  "tags": ["field_transport", "field_childcare"],
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
  "tags": ["budget_general", "local_hino_station"],
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
  "tags": ["welfare_child"],
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

## 🏷 tags（分類体系）

### 全国共通タグ
- `member_tags.json`（議員の専門分野タグ）
- `party_tags.json`（会派の思想タグ）
- `bill_tags.json`（議案・質問の分類タグ）

### 自治体固有タグ
```
spec/tags/local/bill_tags.json
spec/tags/local/party_tags.json
spec/tags/local/member_tags.json
```

---

## 📐 設計思想

- **冗長性ゼロ**  
  同じ情報を複数箇所に持たない

- **関係性は member 側に集約**  
  所属・役職・時系列は member.party_history に統合

- **自治体差は optional で吸収**  
  local タグ、scores の optional 項目など

- **全国展開に耐えるスケール性**  
  ファイルは細かく分割し、巨大ファイルを避ける

---

## 📄 ライセンス

Apache License Version 2.0
