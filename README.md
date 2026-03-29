# 道草ログ GitHub ミラー

このリポジトリは、`sekihan02.github.io` と同じ記事内容を `github.com` 上でも読めるようにした検索向けミラーです。

- 目的: `github.com` ドメインの既存インデックス基盤に載せること
- 原文サイト: [sekihan02.github.io](https://sekihan02.github.io/)
- 代替公開先: [michikusa-log-aji.pages.dev](https://michikusa-log-aji.pages.dev/)
- 補足: GitHub ミラーには記事内チャット機能は含めていません

## 記事一覧

### [第7回: ZIP実装の外にある拡張仕様を整理する](./articles/all-articles.md#zip-edu-07-advanced-topics)

- 公開日: 2026-03-20
- カテゴリ: ZIP
- タグ: ZIP, ZIP64, Deflate, 仕様
- 要約: zip-edu では扱っていない ZIP64 や暗号化なども含め、基本実装の外側にある ZIP の拡張と次の学習ポイントを整理します。

### [第6回: zip-eduをどう読むとZIPの仕組みが分かるか](./articles/all-articles.md#zip-edu-06-repo-walkthrough)

- 公開日: 2026-03-18
- カテゴリ: ZIP
- タグ: ZIP, Deflate, 実装読解, Python
- 要約: ZIP の基本動作を実装した zip-edu を、どの順番で読むと仕組みが分かりやすいか整理します。

### [第5回: ZIP実装を読みながらZIPファイルの中身を理解する](./articles/all-articles.md#zip-edu-05-zip-container)

- 公開日: 2026-03-17
- カテゴリ: ZIP
- タグ: ZIP, CRC32, アーカイブ, バイナリ
- 要約: zip-edu の `zip_format.py` を読みながら、Local File Header から EOCD まで ZIP ファイルの中身を具体的に追います。

### [第4回: ZIP実装を読みながらDeflateのブロックとビット列を理解する](./articles/all-articles.md#zip-edu-04-bitstream-and-blocks)

- 公開日: 2026-03-16
- カテゴリ: ZIP
- タグ: ZIP, Deflate, ビット列, 圧縮
- 要約: zip-edu の `bitstream.py` と `deflate.py` を読みながら、Deflate のブロック構造と LSB-first のビット列を理解します。

### [第3回: ZIP実装を読みながらハフマン符号とDeflateを理解する](./articles/all-articles.md#zip-edu-03-huffman-and-deflate)

- 公開日: 2026-03-15
- カテゴリ: ZIP
- タグ: ZIP, Deflate, Huffman, 圧縮
- 要約: zip-edu の `huffman.py` と `deflate.py` を読みながら、ハフマン符号と Deflate の符号化処理を整理します。

### [第2回: ZIP実装を読みながらLZ77の仕組みを理解する](./articles/all-articles.md#zip-edu-02-lz77)

- 公開日: 2026-03-14
- カテゴリ: ZIP
- タグ: ZIP, Deflate, LZ77, 圧縮
- 要約: zip-edu の `lz77.py` を読みながら、Deflate の前半を担う LZ77 のトークン化と最長一致探索を理解します。

### [第1回: ZIP実装を読む前に、ZIPが何をしている形式か整理する](./articles/all-articles.md#zip-edu-01-overview)

- 公開日: 2026-03-12
- カテゴリ: ZIP
- タグ: ZIP, Deflate, アーカイブ, Central Directory
- 要約: zip-edu を読む前提として、ZIP が圧縮方式ではなく複数ファイルをまとめる形式だと整理します。

### [NDLOCR-LiteのOCR処理を分解してみる](./articles/all-articles.md#ndlocr-lite-ocr-pipeline)

- 公開日: 2026-03-06
- カテゴリ: OCR
- タグ: OCR, NDLOCR-Lite, ONNX, 読み順推定
- 要約: NDLOCR-Lite の OCR がどの順で処理されるかを、責務ごとに整理してみました。

### [道草ログについて](./articles/all-articles.md#michikusa-log-intro)

- 公開日: 2026-03-03
- カテゴリ: 道草ログ
- タグ: 道草ログ, 使い方, チャット
- 要約: このサイトの使い方を短くまとめています。

## このミラーについて

GitHub 上の公開面で記事本文を直接読めるようにするため、各記事を Markdown として配置しています。

最終生成日: 2026-03-29
