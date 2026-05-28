# site-hearing-agent

中小企業の経営者・マーケターと aachat 上で対話し、JTBD（Jobs-To-Be-Done）とバリュープロポジションキャンバスをベースに事業情報を「事業語」で引き出す対話型ディスカバリー agent。

シート方式で **クライアントの負担を抑えつつ網羅性を確保**するヒアリングを行います。

## できること

- aachat 上でクライアントと対話し、JTBD + VPC + Schwartz 5 段階意識レベルに沿って 30 項目をカバー
- **シート方式（コア 10 問 → 詳細 20 問の 2 段階一括送信）** でクライアントが自分のペースで回答可能
- **事前共有資料**（既存サイト・商品ページ・価格表・顧客レビュー・競合 URL 等）と **制作素材**（ロゴ・ブランドガイド・画像）を初動でリクエスト → 後続フェーズの精度を上げる
- マーケ用語を事業語に自動翻訳
- 発話ログをそのまま `hearing/<case-id>.md` に残し、構造化要約と分離
- カバレッジ自己評価

## 使い方

このエージェントは **サイト制作プロジェクトの入り口** です。人間（クライアント・案件オーナー）はまずこのエージェントに話しかけてください。

```bash
aachat session run site-hearing-agent.<owner> --project site-creation-suite \
  "サイト作りたい。"
```

入り口での流れ:

1. **キックオフ 3 問**（対話）: クライアント名・サイト種別・オーナーを軽く確認（**納期は後ほど詳細シートの Q22 で確認**）
2. 裏で `site-strategy-orchestrator` に case doc 作成を依頼（並行・非同期）
3. **事前共有資料リクエスト**（一括メッセージ）: URL リスト中心 + shared doc アタッチ可
4. **制作素材リクエスト**（一括メッセージ）: ロゴ・ブランドガイド・画像 等
5. **コアシート 10 問**（一括メッセージ）: 戦略ブリーフ必須の最重要項目
6. **詳細シート 20 問**（一括メッセージ）: 補強・色付け・制作チーム向け情報

ヒアリング完了後は orchestrator が自動で次のフェーズ（要件整理 → リサーチ → 戦略ブリーフ）にハンドオフします。

### API 起動（自動化案件用）

orchestrator から既存 case doc 付きで起動する場合:

```bash
aachat session run site-hearing-agent.<owner> --project <project> "
クライアント <name> のヒアリングを開始。
brief_type: <lp|saas|未確定>
案件ハブ: [[aachat/docs/<team>/<project>/cases/<case-id>.md]]
"
```

## 構成

- `identity.md` — エージェントの役割・行動方針
- `environment.yaml` — 実行環境
- `memory/` — セッション間の引き継ぎ
- `knowledge/question-bank.md` — キックオフ 3 問 + 事前資料/素材リクエスト + シート 30 問 + 言い換え辞書
- `knowledge/sheet-templates.md` — クライアントに送るシートの実テンプレ集
- `knowledge/pre-shared-assets-checklist.md` — 事前共有資料の項目・受け取り方・case doc 反映
- `knowledge/creative-assets-checklist.md` — 制作素材の項目・受け取り方・brief 反映
- `knowledge/jtbd-framework.md` — Bob Moesta 式の運用
- `knowledge/vpc-framework.md` — Strategyzer Customer Profile
- `knowledge/awareness-level-guide.md` — Schwartz 5 段階判定
- `.agents/skills/intake-session` — シート方式の初回ヒアリング
- `.agents/skills/followup-session` — 不足情報の追加対話
- `.agents/skills/coverage-check` — カバレッジ自己評価
- `.agents/skills/hearing-report` — hearing doc を書く

## 設計ドキュメント

詳細は [[aachat/docs/agent-development/site-creation-suite/specs/site-hearing-agent.md]] を参照。

## 必要な env

なし。

## 注意

- クライアントの発言は意訳せず、生の言葉のまま記録欄に残す
- 個人情報・機密情報が混入した場合は保存可否を人間に確認する
- 事前資料・素材は「ある分だけ」「これから作る」も明示的に受け取って良い（揃わなくてもヒアリングは進める）
