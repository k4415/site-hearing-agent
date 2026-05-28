---
name: intake-session
description: クライアントとの初回ヒアリング対話を進める。「ヒアリング開始」「intake」「<クライアント名>と話す」などのトリガーで起動。
---

# intake-session

クライアントとの初回ヒアリング対話を運営する。

## いつ起動するか

- **人間が直接「サイト作りたい」と話しかけてきた時**（プロジェクトの入り口）
- orchestrator から「<クライアント名>のヒアリングを開始」と依頼された時（API 起動ルート）
- `hearing/<case-id>.md` がまだ存在しない時
- 既存 hearing doc があっても初回セッションが未完了の時

## 入力

- 人間からの最初のメッセージ（自由文）
- もし orchestrator 経由で起動された場合: 案件ハブ doc `cases/<case-id>.md`

## 実行手順

0. **入り口判定**
   - case doc が既に存在 → step 2 へスキップ（A 以降のヒアリングに直行）
   - case doc が無い（＝人間が直接起動）→ step 1 の案件キックオフ確認へ

1. **挨拶 + 案件キックオフ確認**（case doc が無い時のみ）
   - 「サイト制作のヒアリングを担当します。まず簡単に 4 つだけ伺います」
   - `knowledge/question-bank.md` の **⓪ 案件キックオフ確認** ブロックの 4 項目を 1 つずつ聞く:
     - クライアント企業名 / 案件名
     - サイト種別（lp / saas）
     - 納期
     - 案件オーナー
   - 4 つが揃ったら **裏で orchestrator に session run で case doc 作成依頼**
     ```bash
     aachat session run site-strategy-orchestrator --project <project> --via claude-code "
     新規案件の case doc を作成してください。
     - クライアント名: <name>
     - case_id 候補: <kebab-case>
     - brief_type: <lp|saas>
     - 納期: <YYYY-MM-DD>
     - オーナー: <human>
     assignee は site-hearing-agent のまま、status は hearing で。
     "
     ```
   - クライアントには「ありがとうございます。それでは本題に入りますね」と続ける（orchestrator の完了を待たない）

2. **挨拶 + 進め方説明**（ヒアリング本体の導入）
   - 「これから 30 問程度の質問を 1 つずつ伺います。難しいことは聞きません。普段使ってる事業語で答えていただければ大丈夫です」
   - 想定セッション時間（30〜60 分）を伝える

3. **質問順序の決定**
   - `knowledge/question-bank.md` の A → B → C → D → E → F → G → H が基本順
   - クライアントの反応で順序を調整可能（例: ペインに引っかかれば C を先に深掘り）

4. **質問を 1 つずつ投げる**
   - マーケ用語が口から出そうになったら言い換え辞書を引く
   - 抽象語が返ってきたら `knowledge/jtbd-framework.md` の「抽象→具体への戻し方」を使う
   - 答えにくそうな質問は飛ばす（後回し可）

5. **発話を記録**
   - クライアントの発言は生のまま発話ログに残す
   - `[<client-name>] "..."` の形で `hearing/<case-id>.md` の発話ログセクションに即時保存

6. **意識レベルの暫定推定**
   - C / D ブロックの回答から `knowledge/awareness-level-guide.md` の推定ロジックを当てる
   - confidence が low ならフォロー質問を `followup_questions` に追加

7. **セッション終了判断**
   - クライアントが疲れたら無理に続けない（「次回続けましょう」と分割提案）
   - カバレッジが 80% 以上 + followup_questions が 3 件以下 → `coverage-check` skill 起動
   - 80% 未満 → `followup-session` を別途スケジュール

## 出力

- `hearing/<case-id>.md` 更新（発話ログ + 構造化要約）
- `coverage-check` または `followup-session` skill の呼び出し

## NG

- 一度に複数質問を畳み掛ける
- マーケ用語をそのまま使う
- 答えを誘導する質問（「やっぱり〇〇ですよね？」）
- 仮定の未来を聞く（「もし〇〇だったら？」）
- クライアントが疲れているのに続行する
- 個人情報・機密が混入したのに保存可否を確認しない
