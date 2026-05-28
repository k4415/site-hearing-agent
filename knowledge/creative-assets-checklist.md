# Creative Assets Checklist

サイト制作チームが後で困らないよう、**ヒアリング段階で**素材の所在を確認するためのチェックリスト。

## 受け取り方

- **基本**: URL（Google Drive / Notion / Figma / Dropbox 等の共有リンク）
- **補助**: aachat shared doc にアタッチ

「これから作る」「決まっていない」も明示的に回答してもらう（無い情報も後続にとって重要）。

## 項目一覧

| # | カテゴリ | type 値 | 何を集めるか | 後続の活用先 |
|---|---|---|---|---|
| 1 | ロゴ | `logo` | AI / SVG / PNG ファイル。背景透過版を推奨 | デザインエージェント / brief の constraints.mandatory_elements |
| 2 | ブランドガイド | `brand_guide` | ロゴ使用規定・フォント・配色・トーン規定の PDF | デザインエージェント全体・brief の tone |
| 3 | ブランドカラー | `brand_colors` | HEX 値リスト（メイン / サブ / アクセント） | デザイン / brief の tone |
| 4 | フォント | `fonts` | 使用フォント指定（あれば Web フォント URL も） | デザイン |
| 5 | 商品/サービス画像 | `product_images` | 写真 / イラスト の URL or アタッチ | コピー / デザイン |
| 6 | 動画 | `video` | 紹介動画 / お客様の声動画 等の URL | デザイン |
| 7 | 撮影予定素材 | `shoot_planned` | これから撮る予定の素材（何を、いつ） | スケジュール調整 |
| 8 | 必須要素 | `mandatory_badge` | パートナーロゴ / 認証バッジ / 受賞歴 等 | brief.constraints.mandatory_elements に直接反映 |
| 9 | 法定表記 | `compliance` | 特商法 / 薬機法対応文 / プライバシーポリシー URL | brief.constraints.compliance_notes |

## case doc 内での扱い

`cases/<case-id>.md` の frontmatter で:

```yaml
creative_assets:
  - { type: logo, url: "https://drive.google.com/...", status: received }
  - { type: brand_guide, url: "https://...", status: received }
  - { type: brand_colors, value: "#1A73E8 / #F4B400 / #34A853", status: received }
  - { type: mandatory_badge, url: "https://...", description: "ISO27001 認証バッジ", is_mandatory: true, status: received }
  - { type: product_images, status: pending, note: "撮影予定（6月中旬）" }
```

`status` の値:
- `pending`: 「これから作る・撮る」
- `received`: 受領済み
- `not_available`: 無い・該当しない（明示的に確認済み）

## hearing-agent の運用

1. `knowledge/sheet-templates.md` の制作素材シートをクライアントに送る
2. 各項目について「ある」「ない」「これから作る」のいずれかを必ず取る
3. URL 受領後は case doc の `creative_assets` に **session send** で orchestrator に追記依頼
4. すべての必須項目が受領 or 明示的に not_available になったら `coverage.creative_assets_received: done` を立てる

## brief-agent への引き渡し

最終的に brief-agent が:
- `brief.constraints.brand_assets_url` ← `creative_assets[].url` の中で type=logo / brand_guide のもの
- `brief.constraints.mandatory_elements` ← `creative_assets[]` の中で is_mandatory=true のもの（type=mandatory_badge / compliance）
- `brief.tone` ← brand_guide / brand_colors / fonts の情報

を組み立てる。

## NG

- ヒアリングを進める条件にしない（素材が無くても進める）
- 「これから作る」項目を not_available として消さない（pending として残す）
- 著作権が不明な素材を受け取った場合は、クライアントに使用権を確認するよう注意喚起する
