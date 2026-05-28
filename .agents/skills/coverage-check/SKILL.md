---
name: coverage-check
description: hearing doc を読み、何が埋まっていないか自己評価する。「カバレッジ確認」「進捗チェック」などのトリガーで起動。
---

# coverage-check

`hearing/<case-id>.md` を読んで、coverage を自己評価し、次にやるべきこと（追加対話 or 完了ハンドオフ）を判定する。

## いつ起動するか

- セッション終了時
- orchestrator や requirements-agent からカバレッジ確認を依頼された時

## 評価する coverage セル

| セル | 必須 | 完了条件 |
|---|---|---|
| business_overview | 必須 | 事業の一言説明 + 主要商品/プランが埋まっている |
| jtbd_triggers | 必須 | 解決策探し開始のきっかけが 1 つ以上 |
| jtbd_decision_moment | 必須 | 「これだ」と決めた瞬間が具体的に取れている |
| customer_pains | 必須 | 3 つ以上の具体ペイン |
| competitive_alternatives | 必須 | 2 つ以上の代替案 |
| offer | 必須 | 価格 / プラン構造 + 保証/特典 が把握済み |
| customer_gains | 推奨 | 3 つ以上 |
| site_role | 推奨 | 一次 CV + 流入元 + KPI 目標 |
| constraints | 必須 | 公開日 / NG表現 / 既存素材 が確認済み |
| brand_tone | 推奨 | らしさ / NG トーン |
| competitor_references | 推奨 | 参考サイト / 競合サイト |

## 判定基準

- **完了**: 必須セル全 done + followup_questions が 3 件以下 → `hearing-report` skill 起動
- **要追加**: 必須セルに `needs_followup` あり、または followup_questions が 4 件以上 → `followup-session` skill 起動を提案
- **エスカレーション**: 必須セルが取得不能（クライアントが答えない）→ orchestrator に通知

## 出力

`hearing/<case-id>.md` の frontmatter を更新：

```yaml
coverage:
  business_overview: done
  jtbd_triggers: done
  jtbd_decision_moment: needs_followup
  customer_pains: done
  competitive_alternatives: done
  offer: done
  customer_gains: done
  site_role: done
  constraints: done
  brand_tone: needs_followup
  competitor_references: done
followup_questions:
  - "決定打の瞬間の具体エピソード（C社の例）"
  - "NG トーンの具体例"
overall_status: needs_followup  # done | needs_followup | blocked
```

判定結果と次アクションを呼び出し元に返す。

## NG

- 必須セル不足を見逃して完了判定する
- 推奨セルが空のままを「完了」とせず、不要に追加対話を促す
