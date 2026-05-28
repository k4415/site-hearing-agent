# site-hearing-agent

中小企業の経営者・マーケターと aachat 上で対話し、JTBD（Jobs-To-Be-Done）とバリュープロポジションキャンバスをベースに事業情報を「事業語」で引き出す対話型ディスカバリー agent。

## できること

- aachat 上でクライアントと 1〜複数回対話し、JTBD + VPC のフレームに沿って 30 項目程度をカバー
- マーケ用語を事業語に自動翻訳（例: 「ターゲットペルソナ」→「一番来てほしいお客さんを一人」）
- Bob Moesta 式（過去の具体的行動を掘る）の質問運用
- 発話ログをそのまま `hearing/<case-id>.md` に残し、構造化要約と分離
- カバレッジ自己評価（business_overview / jtbd / pains / gains / offer / constraints）

## 使い方

通常は `site-strategy-orchestrator` から `aachat session run` で起動される。

人間が直接起動する場合：

```bash
aachat session run site-hearing-agent --project <project> --via claude-code "
クライアント <name> のヒアリングを開始。
brief_type: <lp|saas|未確定>
案件ハブ: [[aachat/docs/<team>/<project>/cases/<case-id>.md]]
"
```

## 構成

- `identity.md` — エージェントの役割・行動方針
- `environment.yaml` — 実行環境
- `memory/` — セッション間の引き継ぎ
- `knowledge/question-bank.md` — 質問 30 項目 + 事業語言い換え
- `knowledge/jtbd-framework.md` — Bob Moesta 式の運用
- `knowledge/vpc-framework.md` — Strategyzer Customer Profile
- `knowledge/awareness-level-guide.md` — Schwartz 5段階判定
- `.agents/skills/intake-session` — 初回対話
- `.agents/skills/followup-session` — 不足情報の追加対話
- `.agents/skills/coverage-check` — カバレッジ自己評価
- `.agents/skills/hearing-report` — hearing doc を書く

## 設計ドキュメント

詳細は [[aachat/docs/agent-development/site-creation-suite/specs/site-hearing-agent.md]] を参照。

## 必要な env

なし。

## 注意

クライアントの発言は意訳せず、生の言葉のまま記録欄に残す。
個人情報・機密情報が混入した場合は保存可否を人間に確認する。
