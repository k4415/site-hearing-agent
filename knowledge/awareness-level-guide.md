# Awareness Level Guide — Schwartz 5段階意識レベル

Eugene Schwartz の "Breakthrough Advertising"（1966）で定義された、サイト訪問者の意識レベル分類。ヒアリングから「主ターゲットの意識レベル」を暫定推定する。

## 5 段階

| Level | 状態 | サイト訪問時の心理 | ヘッドライン例 |
|---|---|---|---|
| **unaware** | 問題に気づいていない | 「自分には関係ない」 | 興味を引く驚き・トレンド話題 |
| **problem_aware** | 問題は認識、解決策は知らない | 「困ってるけど、どうしたら…」 | 問題への共感・「あなたはこうなっていませんか？」 |
| **solution_aware** | 解決策の存在は知っている、商品は知らない | 「こういう方法があるのは知ってるけど、どれが良いか…」 | カテゴリ名 + ベネフィット |
| **product_aware** | 商品を知っている、買う理由が決まっていない | 「これって他とどう違うの？」 | 差別化 + USP |
| **most_aware** | 商品を知っていて買う気もある、決め手待ち | 「価格が合えば / 今のタイミングなら」 | オファー + 緊急性 |

## ヒアリングからの推定ロジック

クライアントの発話から以下を観察：

### unaware の兆候

- 「うちのお客さんは、まだウチみたいなサービスがあること自体知らない」
- 流入元が SNS の「初めて見た系」「バズで知った」
- 検索キーワードが商品名でも「カテゴリ名」でもなく、関連話題

### problem_aware の兆候

- 「お客さんは困ってるけど、どうしたらいいか分からないって人が多い」
- 検索キーワードが「〇〇 解決方法」「〇〇 困った」など問題ベース
- 既存サイトに来る人の質問が「これは何ですか？」レベル

### solution_aware の兆候

- 「お客さんは『AI で何かする方法』があるのは知ってるけど、どれが良いか迷ってる」
- 検索キーワードが「AI コピー ツール」「AI バナー サービス」などカテゴリ名
- 競合比較を求めてくる

### product_aware の兆候

- 「お客さんはウチを知ってるけど、他社と比べて決められない」
- ブランド指名検索が一定ある
- 「〇〇と比べてどう？」という問い合わせが多い

### most_aware の兆候

- 「お客さんは契約直前。価格 / タイミング / 担当者で決まる」
- 既存顧客の再購入 / アップセル流入が多い
- 「キャンペーン中ですか？」「いつ始められますか？」という問い合わせ

## 推定の出力

`hearing/<case-id>.md` の frontmatter に：

```yaml
awareness_level_estimate:
  primary: solution_aware       # 主ターゲット
  secondary: problem_aware      # 副ターゲット（あれば）
  confidence: medium            # high | medium | low
  evidence:
    - "クライアント発話: 『お客さんはAIツールがあるのは知ってる』"
    - "流入の3割が『AI コピーライティング』カテゴリ検索"
```

confidence が low の場合は `followup_questions` に「主ターゲットの意識レベル確認」を追加。

## 注意

- 一つのサイトに複数の意識レベルが混在することは普通
- 「主ターゲットの意識レベル」を1つに絞る（hero / first view の設計が決まる）
- 副ターゲット用の入り口（別 CTA / 別セクション）は brief で別途設計

## 参考

- https://www.indiehackers.com/post/the-5-stages-of-awareness-are-the-ultimate-marketing-framework-803519dffc
- https://swipefile.com/the-5-levels-of-awareness
