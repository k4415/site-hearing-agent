# Pre-Shared Assets Checklist

クライアントから事前に共有してもらう資料・URL のチェックリスト。**ヒアリングでは拾えない情報を補完するため**に使う。

## 受け取り方

- **基本**: URL リスト（クライアントが既存サイト / Google Drive / Notion / Figma 等にすでに置いている資料を貼ってもらう）
- **補助**: aachat shared doc にアタッチ（PDF / 画像 / ZIP 等）

クライアントには「全部揃わなくて OK、ある分だけ」と必ず明示する。

## 項目一覧と用途

| # | カテゴリ | type 値 | 例 | 後続の活用先 |
|---|---|---|---|---|
| 1 | 既存サイト | `existing_website` | クライアントの自社サイト / LP / サービスページ URL | research-agent の client-asset-parse で deep_scan、要件整理で「現状の言語化」に使う |
| 2 | 商品/サービス紹介 | `product_catalog` | カタログページ / 料金表 / 機能一覧 URL | brief-agent の `offer` / `value_layers` の Feature レイヤーに直接活用 |
| 3 | 提案・営業資料 | `sales_deck` | Google Slides / Notion / PDF URL | brief-agent の `single_minded_proposition` 候補・RTB 候補 |
| 4 | 顧客の声・レビュー・事例 | `voc_review` | 自社サイト掲載 or 外部レビューサイト URL | requirements の VOC キーワード補強、brief-agent の social_proof |
| 5 | 競合サイト | `competitor_ref` | 直接の競合と思うサイト URL（2〜3） | research-agent の competitor-scrape で必須インプット |
| 6 | 参考サイト（好き） | `inspiration_like` | 「こうしたい」と思うサイト URL | requirements の structure.recommended_sections の参考 |
| 7 | 参考サイト（嫌い） | `inspiration_dislike` | 「これにはしたくない」サイト URL | brief-agent の constraints.prohibited に反映 |
| 8 | 業界レポート | `industry_report` | 業界調査・市場動向 URL or PDF | research-agent の market-research の補完 |
| 9 | その他 | `other` | 業務フロー図 / KPI ダッシュボード / 過去キャンペーン記録 等 | 案件次第 |

## case doc 内での扱い

`cases/<case-id>.md` の frontmatter で:

```yaml
pre_shared_assets:
  - { type: existing_website, url: "https://example.com/", status: pending, received_at: "2026-05-28T10:00:00Z" }
  - { type: product_catalog, url: "https://...", status: received }
  - { type: voc_review, url: "https://...", status: received }
  - { type: competitor_ref, url: "https://competitor.com/", status: received }
```

`status` の値:
- `pending`: クライアントが共有予定（まだ URL なし）
- `received`: URL or ファイルを受け取った
- `parsed`: research-agent が client-asset-parse skill で構造化済み

## hearing-agent の運用

1. 上記項目を含むシート（`knowledge/sheet-templates.md` の事前資料シート）をクライアントに送る
2. 回答を受け取ったら、case doc の `pre_shared_assets` に **session send** で orchestrator に追記依頼
3. すべて `status: received` 以上になったら `coverage.pre_shared_assets_received: done` を立てる

## NG

- ヒアリングを進める条件にしない（事前資料が無くてもヒアリングは進める）
- クライアントに「全部揃えてください」とプレッシャーをかけない
- 重い・遅い・要登録のサイトを無理に取りに行かない（後で research-agent が判断する）
- 機密情報（社内 URL / 顧客リストの生データ等）はクライアントが任意で共有するもの。受領後は aachat の機密扱いを守る
