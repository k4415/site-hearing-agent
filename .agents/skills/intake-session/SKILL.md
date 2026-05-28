---
name: intake-session
description: クライアントとの初回ヒアリングをシート方式で進める。「ヒアリング開始」「intake」「サイト作りたい」「<クライアント名>と話す」などのトリガーで起動。
---

# intake-session

クライアントとの初回ヒアリングを **シート方式** で運営する。

実行ステップは:
**キックオフ 3 問（対話）→ 事前資料リクエスト（一括）→ 制作素材リクエスト（一括）→ コアシート 10 問（一括）→ 詳細シート 20 問（一括）**

## いつ起動するか

- **人間が直接「サイト作りたい」と話しかけてきた時**（プロジェクトの入り口）
- orchestrator から「<クライアント名>のヒアリングを開始」と依頼された時（API 起動ルート）
- `hearing/<case-id>.md` がまだ存在しない時
- 既存 hearing doc があっても初回セッションが未完了の時

## 入力

- 人間からの最初のメッセージ（自由文）
- もし orchestrator 経由で起動された場合: 案件ハブ doc `cases/<case-id>.md`

## 実行手順

### 0. 入り口判定

- case doc が既に存在 → **step 4 へスキップ**（資料・素材リクエストから開始）
- case doc が無い（＝人間が直接起動）→ step 1 から

### 1. 挨拶 + キックオフ 3 問（対話形式、1 問ずつ）

`knowledge/question-bank.md` の **⓪ 案件キックオフ確認** ブロック + `knowledge/sheet-templates.md` の ⓪ テンプレに従って、対話で 3 問だけ聞く:

1. クライアント企業名 / 案件名
2. サイト種別（lp / saas）
3. 案件オーナー

**納期はここでは聞かない**（B/H ブロックの Q22 で会話の流れで確認する）。

### 2. 裏で orchestrator に case doc 作成依頼（非同期）

3 つが揃った瞬間に、クライアントを待たせず **裏で session run**:

```bash
aachat session run site-strategy-orchestrator --project <project> --via claude-code "
新規案件の case doc を作成してください。
- クライアント名: <name>
- case_id 候補: <kebab-case>
- brief_type: <lp|saas>
- オーナー: <human-username>
- 現フェーズ: hearing（私が継続中）

期待: cases/<case-id>.md を作成。
- assignee は site-hearing-agent のまま
- status は hearing
- due_date は未確定（後で hearing-agent から session send で追記する）
- pre_shared_assets: []
- creative_assets: []
"
```

orchestrator の完了通知は待たない。

### 3. つなぎメッセージ

クライアントに「ありがとうございます。それでは事前資料の確認に進みますね」と返す。

### 4. 事前共有資料リクエスト（一括メッセージ）

`knowledge/sheet-templates.md` の **① 事前共有資料リクエスト** テンプレを **1 メッセージで送信**。

回答受信後:
- 各 URL を整理し、case doc の `pre_shared_assets` に追記依頼:
  ```bash
  aachat session run site-strategy-orchestrator --project <project> --via claude-code "
  cases/<case-id>.md の pre_shared_assets に以下を追記してください:
  - { type: existing_website, url: '<url>', status: received }
  - { type: competitor_ref, url: '<url>', status: received }
  ... (全件)
  "
  ```
- 「ありがとうございます。続けて素材の確認に進みますね」と返して step 5 へ

### 5. 制作素材リクエスト（一括メッセージ）

`knowledge/sheet-templates.md` の **② 制作素材リクエスト** テンプレを **1 メッセージで送信**。

回答受信後:
- 各項目を case doc の `creative_assets` に追記依頼（同様に session run）
- 「ありがとうございます。それでは本ヒアリングに入りますね」と返して step 6 へ

### 6. コアシート 10 問（一括メッセージ）

`knowledge/sheet-templates.md` の **③ コアシート 10 問** テンプレを **1 メッセージで送信**。

回答受信後:
- クライアントの発言を生のまま `hearing/<case-id>.md` の発話ログに記録（`[<client-name>] "..."` 形式）
- 構造化要約セクションを更新
- 回答が抽象的すぎる項目があれば、`followup_questions` に深掘りメモを追加
- coverage の主要セルを評価

### 7. 詳細シート 20 問（一括メッセージ）

`knowledge/sheet-templates.md` の **④ 詳細シート 20 問** テンプレを **1 メッセージで送信**。

回答受信後:
- 同様に発話ログ記録、構造化要約更新
- coverage を再評価
- Q22 で得た納期を orchestrator に session send で case doc に追記
  ```bash
  aachat session run site-strategy-orchestrator --project <project> --via claude-code "
  cases/<case-id>.md の due_date を '<YYYY-MM-DD>' に更新してください。
  "
  ```

### 8. 意識レベルの暫定推定

詳細シートの Q15〜Q18 等から `knowledge/awareness-level-guide.md` の推定ロジックを当てる。confidence が low ならフォロー質問を `followup_questions` に追加。

### 9. セッション終了判断

- 主要 6 セル（business_overview / jtbd_triggers / jtbd_decision_moment / customer_pains / competitive_alternatives / offer）+ pre_shared_assets_received / creative_assets_received が全て done → `coverage-check` skill 起動 → `hearing-report` で完了
- 不足セルあり → `followup-session` で再質問
- クライアントが「全部書くのは大変」と言った場合: コアシートだけ埋まれば次に進める旨を伝え、詳細シートは任意で対応

## 出力

- `hearing/<case-id>.md` 更新（発話ログ + 構造化要約 + 事前資料/素材セクション）
- `cases/<case-id>.md` 更新（pre_shared_assets / creative_assets / due_date 追記、orchestrator 経由）
- `coverage-check` または `followup-session` または `hearing-report` skill の呼び出し

## 完了時のメッセージ例

`knowledge/sheet-templates.md` の **⑤ 完了通知** テンプレを使う。

## NG

- **キックオフで納期を聞かない**（B/H ブロックの Q22 で会話の流れで確認）
- **本ヒアリング 30 問を 1 問ずつ畳み掛けない**（必ずシート方式で一括送信）
- 答えを誘導する質問（「やっぱり〇〇ですよね？」）
- 仮定の未来を聞く（「もし〇〇だったら？」）
- クライアントが疲れているのに続行する
- 個人情報・機密が混入したのに保存可否を確認しない
- 資料/素材が揃っていないことを理由に本ヒアリングを止めない
