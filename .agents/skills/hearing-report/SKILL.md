---
name: hearing-report
description: 発話ログと構造化要約を hearing/<case-id>.md に保存し、ハンドオフに備える。「ヒアリング結果を保存」「レポート化」などのトリガーで起動。
---

# hearing-report

発話ログと構造化要約を `hearing/<case-id>.md` に保存し、次フェーズに渡せる状態にする。

## いつ起動するか

- `coverage-check` が「完了」と判定した時
- 各セッション終了時（中間保存）

## ファイル構造

```yaml
---
title: "<案件名> ヒアリング結果"
status: done                    # in_progress | done | needs_followup
case_id: <case-id>
brief_type: lp | saas | 未確定
session_count: 3
last_updated: <ISO8601>
coverage:
  business_overview: done
  jtbd_triggers: done
  jtbd_decision_moment: done
  customer_pains: done
  competitive_alternatives: done
  customer_gains: done
  offer: done
  site_role: done
  constraints: done
  brand_tone: done
  competitor_references: done
awareness_level_estimate:
  primary: solution_aware
  secondary: problem_aware
  confidence: medium
  evidence: ["..."]
followup_questions: []
---

# ヒアリング結果

## 1. 事業概要（事業語そのまま）

<クライアントの言葉そのまま>

## 2. JTBD（決定打）

### Trigger（探し始めたきっかけ）
- [<client-name>] "..."

### Decision moment（「これだ」と決めた瞬間）
- [<client-name>] "..."
- いた場所、見ていたもの、誰と一緒にいたか:

### Competitive alternatives（検討した代替案）
- 1. ...
- 2. ...

## 3. ペイン / ゲイン（VPC Customer Profile）

### Pains（顧客が嫌がっていること）
- 機能的: ...
- 社会的: ...
- 感情的: ...

### Gains（顧客が望んでいること）
- 期待: ...
- 必須: ...
- 望み: ...

## 4. オファー（offer）

- 商品: ...
- 価格: ...
- 保証: ...

## 5. サイトの役割（site_role）

- 一次 CV: ...
- 流入元: ...
- KPI 目標: ...

## 6. 制約・参考

- 公開日: ...
- NG: ...
- 既存素材: ...
- 参考サイト: ...

## 7. ブランド・トーン

- らしさ: ...
- NG トーン: ...

## 8. 発話ログ（生データ）

### Session 1 (<日時>)
- [<timestamp>] [client] "..."
- [<timestamp>] [agent] "..."
...

### Session 2 (<日時>)
...
```

## 実行手順

1. **既存 doc を読む**（あれば追記、なければ新規作成）

2. **構造化要約を更新**
   - 各ブロックで取得した情報を該当セクションに入れる
   - クライアントの生の言葉は **意訳せず** `[<client-name>] "..."` の形で引用

3. **発話ログを更新**
   - セッション単位で時系列に整理
   - 個人情報・機密情報が混入していたら redact または保存可否を確認

4. **frontmatter の coverage を更新**

5. **status を判定**
   - 全必須セル done + followup 3 件以下 → `status: done`
   - そうでなければ `status: needs_followup`

6. **ハンドオフ（done の場合）**
   - case ハブ doc の `status: requirements`、`assignee: site-requirements-agent` に更新
   - case ハブ doc の `children` に hearing doc を追加
   - orchestrator にハンドオフ通知（mention）

## NG

- 発話ログを要約してしまう（生の言葉を失う）
- 構造化要約に解釈・仮説を混ぜる
- frontmatter を更新せず本文だけ書く
- 個人情報を確認せず保存
