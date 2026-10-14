# 実例: 取材テキストから提出物へ

架空の「株式会社ミナト電子工業」(法人番号 9050005000055)の例。タスクJSONの `master` は全フィールド `unresearched` / `null`、`priority_fields` は8フィールド全部、とする。

## 取材

検索で候補 `https://www.minato-denshi.example/` を見つけ、gBizInfo 法人個別ページの住所(大阪府大阪市西区…)と会社概要の所在地が一致することを確認して公式サイトと確定。fetch は4回(gBizInfo・トップ・会社概要・お問い合わせ)+語彙確認1回 — 取得に成功した5URLを fetched_urls に記録する。

**先に: gBizInfo 法人個別ページ** fetch `https://info.gbiz.go.jp/hojin/ichiran?hojinBango=9050005000055`:
住所「大阪府大阪市西区…」と代表者名を控える(候補の住所照合用)。

**1回目: トップページ** fetch `https://www.minato-denshi.example/`(抜粋):

```
# 株式会社ミナト電子工業
産業用組み込みシステムの開発・受託
# 株式会社ミナト電子工業
産業用組み込みシステム・制御盤の設計開発
製品ラインナップ
- 組み込み制御ソフトウェア
- 産業用ロボット制御盤
- 遠隔監視システム
法人のお客様へ - 製造現場の省人化をご提案します
会社概要 <https://www.minato-denshi.example/company/>
お問い合わせ <https://www.minato-denshi.example/contact/>
株式会社ミナト電子工業 | 〒550-0000 大阪府大阪市西区… | TEL 06-5555-1140
```

読み取り: 製品3項目(keywords の元)、「法人のお客様へ」(features の元: BtoB)、会社概要とお問い合わせのリンク、フッターに代表TEL。

**2回目: 会社概要** fetch `https://www.minato-denshi.example/company/`(抜粋):

```
# 会社概要 | 株式会社ミナト電子工業
会社概要
会社名 | 株式会社ミナト電子工業
設立 | 2005年4月
資本金 | 3,000万円
代表者 | 港 太郎
従業員数 | 42名
事業内容 | 組み込みシステムの受託開発、制御盤の設計・製造
FAX | 06-5555-1141
```

読み取り: 設立・代表者・従業員数は**管理フィールドで提案しない**。資本金もこのタスクの `supplemental_fields` が空(=gBizInfoに値がある)のため提案しない(条件付き補充 — タスクJSONのフラグがあるときだけ会社概要の資本金を円換算で提案できる。ADR 0023)。「事業内容」は読点区切りの文なので keywords には加えない(keywords はトップの製品ラインナップ列挙から)。FAXを確認。上場市場はクロール対象外(#153)。

**3回目: お問い合わせ** fetch `https://www.minato-denshi.example/contact/`(抜粋):

```
# お問い合わせ | 株式会社ミナト電子工業
お問い合わせ
以下のフォームよりお問い合わせください。
メールでも受け付けています: support@minato-denshi.example
採用のご応募は採用専用フォーム(requ... 
```

読み取り: 問い合わせフォームの存在(`links.contact` = このページ)、一般窓口のメールアドレス。採用専用は無視。

業界語彙の確認も行う: corpdex サイトの `/industries`(業界一覧)をfetch → リンクURLから `communication-equipment`(情報通信機械製造)など語彙IDを読む。この会社は受託開発が主で、語彙では「情報通信機械製造」または粗の「IT」が近い — ここでは細語彙が合わないため粗の `it` を選んだ、のように**細を優先し、合わなければ粗**。

## 提出物

`task_submit` ツールの引数(`company_id` はタスクJSONの法人番号、`fetched_urls` はこのタスクで取得に成功したURL一覧 — 語彙確認の corpdex `/industries` も含む。[schema/submission.schema.json](../schema/submission.schema.json) は changes 各要素の形状の対象):

```json
{
  "company_id": "9050005000055",
  "fetched_urls": [
    "https://info.gbiz.go.jp/hojin/ichiran?hojinBango=9050005000055",
    "https://www.minato-denshi.example/",
    "https://www.minato-denshi.example/company/",
    "https://www.minato-denshi.example/contact/",
    "https://www.corpdex.example/industries"
  ],
  "changes": [
    {
      "field": "links.contact",
      "status": "present",
      "value": "https://www.minato-denshi.example/contact/",
      "source_url": "https://www.minato-denshi.example/"
    },
    {
      "field": "contact.phone",
      "status": "present",
      "value": "06-5555-1140",
      "source_url": "https://www.minato-denshi.example/"
    },
    {
      "field": "contact.fax",
      "status": "present",
      "value": "06-5555-1141",
      "source_url": "https://www.minato-denshi.example/company/"
    },
    {
      "field": "contact.email",
      "status": "present",
      "value": "support@minato-denshi.example",
      "source_url": "https://www.minato-denshi.example/contact/"
    },
    {
      "field": "industries",
      "status": "present",
      "value": ["it"],
      "source_url": "https://www.minato-denshi.example/company/",
      "note": "組み込みシステムの受託開発が主業。細語彙に一致なしのため粗it"
    },
    {
      "field": "keywords",
      "status": "present",
      "value": [
        "組み込み制御ソフトウェア",
        "産業用ロボット制御盤",
        "遠隔監視システム"
      ],
      "source_url": "https://www.minato-denshi.example/"
    },
    {
      "field": "features",
      "status": "present",
      "value": ["BtoB"],
      "source_url": "https://www.minato-denshi.example/"
    }
  ]
}
```

`type` / `schema_version` は渡さない(サーバー側で処理される)。422 が返ったら issues を読んで修正し、再提出する。

## この例のポイント

- `company_id` はタスクJSONの `corporate_number`、`fetched_urls` は出典URLをすべて含む(出典が記録にない提出は機械検証で拒否される)
- 管理フィールド(設立・代表者・従業員数)はテーブルにあっても1つも入れていない。資本金は `supplemental_fields` が空のため同じく対象外
- 電話・FAXはラベルを除いた番号本体。複数候補(採用専用)には触れていない
- keywords は製品ラインナップの**原文どおり**3項目のみ(事業内容の文から語を作っていない)
- 「サイトリニューアル中で何も情報がない」ページだけの企業なら: `links.contact` / `contact.phone` / `contact.fax` / `contact.email` は `absent`(出典はそのページ)。リスト3種は提案しない
