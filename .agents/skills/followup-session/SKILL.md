---
name: followup-session
description: 前回未確認だった followup_questions を解消する追加ヒアリング対話。「追加ヒアリング」「不足情報を聞く」などのトリガーで起動。
---

# followup-session

前セッションで残った `followup_questions` だけに集中して追加ヒアリングする。

## いつ起動するか

- `coverage-check` skill が「カバレッジ < 80%」と判定し、追加対話が必要な時
- クライアントが「明日続きを」と分割を希望した時
- 別フェーズ（requirements-agent）から「<項目>が不足している」と再依頼された時

## 入力

- 既存 `hearing/<case-id>.md`（特に `followup_questions` 配列と `coverage`）

## 実行手順

1. **既存 doc 読み込み**
   - `followup_questions` を全件確認
   - coverage の `needs_followup` セルを確認

2. **質問の優先順位付け**
   - 必須セル（business_overview / jtbd_decision_moment / customer_pains / competitive_alternatives / offer）の不足を最優先
   - 副次セル（ブランドトーン / 参考サイト）は後回し可

3. **挨拶 + 状況確認**
   - 「前回の続きを聞かせてください。残ってる質問は <件数> 個です」
   - 想定時間（10〜30 分）を伝える

4. **質問を進める**
   - intake-session と同じルール（一度に 1 問、生の言葉を残す、抽象→具体に戻す）
   - 全 followup を埋めたら終了

5. **再カバレッジチェック**
   - `coverage-check` skill 起動
   - 主要 6 セルが done になったら hearing 完了 → `hearing-report` skill 起動

## NG

- 既存 doc を読まずに同じ質問をする
- followup 以外の質問で時間を使う
- カバレッジ 80% に達したのに追加で延々と聞く
