# 道草ログ GitHub ミラー 全文

原文サイトの記事本文を、GitHub 上でも続けて読めるようにまとめています。

<a id="zip-edu-07-advanced-topics"></a>

## 第7回: ZIP実装の外にある拡張仕様を整理する

- 公開日: 2026-03-20
- 更新日: 2026-03-20
- カテゴリ: ZIP
- タグ: ZIP, ZIP64, Deflate, 仕様
- 原文サイト: [sekihan02.github.io](https://sekihan02.github.io/articles/zip-edu-07-advanced-topics/)

> zip-edu では扱っていない ZIP64 や暗号化なども含め、基本実装の外側にある ZIP の拡張と次の学習ポイントを整理します。

ここまで読んできた [zip-edu](https://github.com/sekihan02/zip-edu/tree/master) は、ZIP の基本動作をライブラリに頼らず実装した学習用リポジトリです。

この最終回では、その実装があえて扱っていない拡張仕様も含めて、ZIP 全体をどこまで理解できたかを整理します。

## まず連載全体の地図

この連載は、ZIP を次の順で追っていきます。

1. 第1回: ZIP 全体の役割と流れをつかむ
2. 第2回: LZ77 で繰り返しをトークンに変える
3. 第3回: ハフマン符号でトークンを短いビット列にする
4. 第4回: Deflate をブロックとビット列として組み立てる
5. 第5回: できたデータを ZIP コンテナに入れる
6. 第6回: ここまでの仕組みがリポジトリのどこにあるか整理する
7. 第7回: 基本実装の外側にある拡張仕様を見る（今回）

この第7回は、連載のまとめと次の入口に当たる回です。  
ここまでで見てきた基本の ZIP を土台として、その外側にある ZIP64 や暗号化や追加の圧縮方式が、どこから話を広げるものなのかを整理します。

## この回で答える問い

- このリポジトリが扱わない ZIP の拡張を知る
- 「ZIP を理解した」と言うために、どこまで知っておくべきか整理する
- 次に何を読むべきかを示す
- 基本の ZIP の外側に、どんな拡張が広がっているのか整理する

## 先に答えると

このリポジトリを読み切ると、ZIP の幹は理解できます。

- ZIP コンテナ
- Deflate
- LZ77
- ハフマン
- CRC32
- Central Directory
- EOCD

ただし「ZIP の全仕様」を完全に実装したことにはなりません。  
ZIP は長い歴史の中で拡張されてきたからです。

## 拡張仕様の見取り図

この連載で扱ったのは、ZIP の土台になる部分です。

1. ファイルを並べる ZIP コンテナ
2. 圧縮方式としての Deflate
3. 整合性確認の CRC32
4. 末尾の EOCD と中央ディレクトリ

その外側には、さらに次の拡張があります。

- 大きなサイズに対応する ZIP64
- 内容を保護する暗号化
- Deflate 以外の圧縮方式
- raw DEFLATE, zlib, gzip など周辺形式との違い

この最終回では、基本の ZIP を押さえたうえで、その外側に何があるのかを整理します。

## ZIP64

普通の ZIP のサイズ欄やオフセット欄は 32 ビットです。  
大きなファイルや巨大アーカイブではこれでは足りません。

そこで ZIP64 では追加レコードを使って、より大きな値を持てるようにします。[^appnote-zip64]

このリポジトリの README でも ZIP64 は未対応と明記されています。  
そのため、大容量対応は次の学習テーマになります。

## 暗号化

APPNOTE には暗号化や strong encryption の記述もあります。[^appnote-encryption]
しかし、このリポジトリは暗号化を扱いません。

暗号化が入ると、

- 圧縮
- コンテナ
- 鍵管理
- 認証

が混ざってしまい、本質が見えにくくなるからです。

## 追加の圧縮方式

ZIP は Deflate だけではありません。  
APPNOTE の compression method 一覧には、Store, Deflate 以外にも BZIP2, LZMA などが載っています。[^appnote-method]

ただし互換性を考えると、現実の基礎学習では

- method 0 Store
- method 8 Deflate

を先に押さえるのが現実的です。

## raw DEFLATE と zlib / gzip の違い

ここは実装者が混乱しやすい場所です。

![Wrapper compare](https://sekihan02.github.io/images/articles/zip-edu-07-advanced-topics/07_wrapper_compare.svg)

- raw DEFLATE
  - 圧縮ブロック本体だけ
- zlib
  - DEFLATE + zlib header + Adler-32[^rfc1950]
- gzip
  - DEFLATE + gzip header + CRC32 + ISIZE[^rfc1952]

ZIP の file data に入る Deflate は、普通は raw DEFLATE と考えるのが自然です。  
このリポジトリもその前提で作られています。

## 次に学ぶなら何か

### 課題1

`explain-zip` の出力を見ながら `sample.zip` の EOCD を手で読む。

### 課題2

`abracadabra` ではなく、もっと長い英文で

- `stored`
- `fixed`
- `dynamic`

のどれが勝つかを観察する。

### 課題3

`lz77.py` を高速化する案を考える。

### 課題4

ZIP64 を自前追加するとしたら、どの構造体に何を足すか設計する。

## 「ZIP を理解した」と言える状態

次の問いに、自分の言葉で答えられればかなり強いです。

1. ZIP は圧縮形式か、アーカイブ形式か
2. Deflate は何と何の組み合わせか
3. なぜ Central Directory が必要か
4. Data Descriptor は何のためにあるか
5. `stored / fixed / dynamic` はどう違うか
6. CRC32 は圧縮率のためか、整合性確認のためか
7. raw DEFLATE と zlib/gzip はどう違うか

この7問に答えられれば、少なくとも「ブラックボックスとして ZIP を使っている状態」からは抜けています。

## 最後にもう一度答えると

- `zip-edu` を読むと、ZIP の幹にある仕組みはかなり理解できます。
- ただし ZIP64、暗号化、追加圧縮方式などは別途学ぶ必要があります。
- それでも、ZIP をブラックボックスとして使う段階からは十分抜け出せます。

## 連載の締め

この連載では、

- ZIP は箱である
- 後ろに目次がある
- Deflate は LZ77 + Huffman でできている

という前提を、コードとビット列まで降ろして確認してきました。

ZIP は一見すると古い形式ですが、実装してみると

- データ構造
- 可逆圧縮
- ビット IO
- 整合性検証
- 互換性設計

が詰まった、とても良い教材です。

## 参考

[^appnote-zip64]: PKWARE, "APPNOTE.TXT", sections 4.3.14, 4.3.15 and related ZIP64 records. https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT
[^appnote-encryption]: PKWARE, "APPNOTE.TXT", section 6 and related records. https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT
[^appnote-method]: PKWARE, "APPNOTE.TXT", section 4.4.5. https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT
[^rfc1950]: RFC 1950. https://www.rfc-editor.org/rfc/rfc1950
[^rfc1952]: RFC 1952. https://www.rfc-editor.org/rfc/rfc1952

---

原文 URL: https://sekihan02.github.io/articles/zip-edu-07-advanced-topics/

---

<a id="zip-edu-06-repo-walkthrough"></a>

## 第6回: zip-eduをどう読むとZIPの仕組みが分かるか

- 公開日: 2026-03-18
- 更新日: 2026-03-18
- カテゴリ: ZIP
- タグ: ZIP, Deflate, 実装読解, Python
- 原文サイト: [sekihan02.github.io](https://sekihan02.github.io/articles/zip-edu-06-repo-walkthrough/)

> ZIP の基本動作を実装した zip-edu を、どの順番で読むと仕組みが分かりやすいか整理します。

この連載で扱っている [zip-edu](https://github.com/sekihan02/zip-edu/tree/master) は、ZIP の基本動作を `zipfile` や `zlib` に頼らずスクラッチで実装した学習用リポジトリです。

この回では、ここまで読んできた各部品がリポジトリ全体のどこにあるのかを整理し、どの順に読むと ZIP の仕組みが頭に入りやすいかをまとめます。

## まず連載全体の地図

この連載は、ZIP を次の順で追っていきます。

1. 第1回: ZIP 全体の役割と流れをつかむ
2. 第2回: LZ77 で繰り返しをトークンに変える
3. 第3回: ハフマン符号でトークンを短いビット列にする
4. 第4回: Deflate をブロックとビット列として組み立てる
5. 第5回: できたデータを ZIP コンテナに入れる
6. 第6回: ここまでの仕組みがリポジトリのどこにあるか整理する（今回）
7. 第7回: 基本実装の外側にある拡張仕様を見る

この第6回は、ここまで分けて見てきた部品を、コード全体の中でつなぎ直す回です。  
ZIP 全体で見たときに、どのファイルがどの役割を持ち、圧縮と展開の流れがリポジトリ内でどうつながるかを整理します。

## この回で答える問い

- 仕様とコードの対応をつける
- どの順にファイルを読むと理解しやすいかを示す
- pack と unpack が、どの部品をどんな順で通るのかを整理する

## 先に答えると

- 読み始める順番は、小さい部品から大きい部品へ進むのが分かりやすいです。
- ZIP の本質を見るなら `zip_format.py` と `deflate.py` が中心です。
- `explain` 系コマンドとテストを見ると、実装の意図がかなり早くつかめます。

## リポジトリ全体の流れ

このリポジトリは、次の層に分かれています。

1. `bitstream.py`
   - ビットを読む・書く
2. `lz77.py`
   - 文字列を `literal` と `match` に分ける
3. `huffman.py`
   - シンボルへ符号を割り当てる
4. `deflate.py`
   - Deflate のビット列を作る・読む
5. `crc32.py`
   - 整合性確認の値を計算する
6. `zip_format.py`
   - ZIP の各レコードを組み立てる・読む
7. `service.py` と `cli.py`
   - 外から呼び出す入口になる

要するに、下の層ほど部品に近く、上の層ほど「ZIP として使う」形に近づきます。

![Repository map](https://sekihan02.github.io/images/articles/zip-edu-06-repo-walkthrough/06_code_map.svg)

## 圧縮の流れ

圧縮パスは、だいたい次の関数列です。

1. `cli.py`
   - `pack`
2. `service.py`
   - `pack_zip`
3. `zip_format.py`
   - `build_zip`
4. `zip_format.py`
   - `_compress_data`
5. `deflate.py`
   - `compress_deflate`
6. `lz77.py`
   - `lz77_encode`
7. `huffman.py`
   - 符号長と正準符号の生成

CLI は入口です。  
中核になるのは `zip_format.py` と `deflate.py` です。

## 展開の流れ

展開パスは逆向きです。

1. `cli.py`
   - `unpack`
2. `service.py`
   - `unpack_zip`
3. `zip_format.py`
   - `parse_central_directory`
4. `zip_format.py`
   - `extract_all`
5. `deflate.py`
   - `decompress_deflate`
6. `crc32.py`
   - CRC 検証

この流れを見ると、ZIP の展開は

- まず箱を読む
- その後で圧縮方式ごとの展開へ降りる

という順番だということです。

## どこを見ると意図が分かるか

このリポジトリで意図をつかみやすいのは、次の3点です。

- 圧縮ライブラリに頼っていない
  - 仕様の概念がそのまま関数に出てくる
- 層が分かれている
  - bit IO / LZ77 / Huffman / Deflate / ZIP コンテナを分けて読める
- `explain` がある
  - 読むだけでなく、実行して確かめられる

## この実装で単純化している部分

- LZ77 探索はナイーブ
  - 高速化より理解を優先している
- `auto` は実際に全部作って比較する
  - 理論値ではなく、できた結果のサイズで選ぶ
- GUI をコアから外している
  - ZIP の理解に必要な部分だけを追いやすい

## テストはどこを見るべきか

[tests](https://github.com/sekihan02/zip-edu/tree/master/tests) は、理解を確かめるうえでも重要です。

- `test_lz77.py`
  - 距離と長さの制約
- `test_deflate.py`
  - `stored / fixed / dynamic` の往復
- `test_zip_format.py`
  - `zipfile` 互換性
- `test_explain.py`
  - 学習用説明出力の確認
- `test_project_intent.py`
  - コアが圧縮ライブラリに依存していないことの確認

テストは、単なる品質保証だけでなく、「この実装が何を大事にしているか」を示すものでもあります。

## 実装していないもの

ここは正直に把握した方が理解が深まります。

- ZIP64
- 暗号化
- 分割 ZIP
- Deflate 以外の実装
- 高速辞書探索
- 仕様の細かな拡張フィールド全部

ただ、これは弱点というより「教材としてそう切っている」ということです。

## ここから実際にファイルを見る

おすすめの読み順は次です。

1. [bitstream.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/bitstream.py)
2. [lz77.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/lz77.py)
3. [huffman.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/huffman.py)
4. [deflate.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/deflate.py)
5. [crc32.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/crc32.py)
6. [zip_format.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/zip_format.py)
7. [service.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/service.py)
8. [cli.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/cli.py)
9. [tests](https://github.com/sekihan02/zip-edu/tree/master/tests)

この順で読むと、小さい部品から大きい部品へ無理なく追えます。

## 最後にもう一度答えると

- このリポジトリは ZIP 学習用として読みやすい
- コアロジックは層ごとに分かれている
- explain 系コマンドで動かしながら理解できる
- 未実装範囲を知ることで、逆に実装範囲の輪郭がはっきりする

次回は、未実装部分も含めて ZIP 全体の地図を整理します。

---

原文 URL: https://sekihan02.github.io/articles/zip-edu-06-repo-walkthrough/

---

<a id="zip-edu-05-zip-container"></a>

## 第5回: ZIP実装を読みながらZIPファイルの中身を理解する

- 公開日: 2026-03-17
- 更新日: 2026-03-17
- カテゴリ: ZIP
- タグ: ZIP, CRC32, アーカイブ, バイナリ
- 原文サイト: [sekihan02.github.io](https://sekihan02.github.io/articles/zip-edu-05-zip-container/)

> zip-edu の `zip_format.py` を読みながら、Local File Header から EOCD まで ZIP ファイルの中身を具体的に追います。

[zip-edu](https://github.com/sekihan02/zip-edu/tree/master) の `zip_format.py` と実際の `sample.zip` を見比べながら、ZIP ファイルの中に何がどの順で入っているのかを確認します。

## まず連載全体の地図

この連載は、ZIP を次の順で追っていきます。

1. 第1回: ZIP 全体の役割と流れをつかむ
2. 第2回: LZ77 で繰り返しをトークンに変える
3. 第3回: ハフマン符号でトークンを短いビット列にする
4. 第4回: Deflate をブロックとビット列として組み立てる
5. 第5回: できたデータを ZIP コンテナに入れる（今回）
6. 第6回: ここまでの仕組みがリポジトリのどこにあるか整理する
7. 第7回: 基本実装の外側にある拡張仕様を見る

この第5回は、圧縮データの外側を包む ZIP コンテナを見る回です。  
ここまでの回で見てきた Deflate データが、Local File Header や Central Directory や EOCD と一緒にどう並んで、1つの ZIP ファイルになっているのかを扱います。

## この回で答える問い

- Local File Header, Data Descriptor, Central Directory, EOCD を見分ける
- little-endian とオフセットの意味を理解する
- CRC32 がなぜ必要かを知る
- ZIP 全体では、これらのレコードがどんな順で並ぶのか

## 先に答えると

- ZIP の前半には各ファイルのローカル情報とデータ本体が並びます。
- 後半には中央ディレクトリと EOCD があり、一覧や位置の確認は主にこちらを使います。
- CRC32 は圧縮率のためではなく、展開した結果が正しいか確認するためにあります。
- つまり ZIP は、前半に各ファイルの実体、後半に全体の目次を持つ形式です。

## ZIP コンテナ全体の流れ

ZIP 全体で並んでいるものは、次の5段階で考えると分かりやすいです。

1. 各ファイルごとに Local File Header を書く
2. その直後に圧縮データや生データを書く
3. 必要なら Data Descriptor を後ろに付ける
4. すべてのファイルが終わったあと、Central Directory をまとめて書く
5. 最後に EOCD を置いて、目次の開始位置と件数を示す

読む側はこの逆で、まず最後の EOCD から中央ディレクトリへたどり、そこから各ファイルの本体へ移動します。

## まずは全体図

![ZIP container](https://sekihan02.github.io/images/articles/zip-edu-05-zip-container/05_zip_container.svg)
![Sample ZIP offsets](https://sekihan02.github.io/images/articles/zip-edu-05-zip-container/05_sample_zip_offsets.svg)

このリポジトリのサンプル ZIP は、2つのファイルを次の順で並べています。

1. `input/a.txt` の Local File Header
2. `input/a.txt` の Deflate data
3. `input/a.txt` の Data Descriptor
4. `input/deep/b.txt` の Local File Header
5. `input/deep/b.txt` の Deflate data
6. `input/deep/b.txt` の Data Descriptor
7. Central Directory
8. EOCD

APPNOTE でも、ZIP の全体構造はこの考え方で説明されています。[^appnote-container]

2枚目の図は、このリポジトリで実際に作った `sample.zip` のオフセットをそのまま描いたものです。

- `0` バイト目から最初のローカルヘッダ
- `143` バイト目から中央ディレクトリ
- `262` バイト目から EOCD

記事に出てくる数字を、図でも追えるようにしています。

## little-endian はどう効いているか

ZIP は little-endian です。[^appnote-endian]

このリポジトリは `struct.pack("<...")` と `struct.unpack_from("<...")` を使っていて、  
`<` が little-endian を意味します。

## Data Descriptor の意味

今回のサンプル ZIP は `--data-descriptor` を付けて作っています。  
そのため、Local File Header の CRC とサイズは最初は 0 で書かれ、後ろに Data Descriptor が付きます。

APPNOTE の general purpose bit flag では、bit 3 が立っているとき、

- local header の CRC-32
- compressed size
- uncompressed size

は 0 で埋められ、データの後ろにある descriptor に実際の値を書くと説明されています。[^appnote-bit3]

この実装でも、`build_zip(..., use_data_descriptor=True)` では

- ヘッダ側はゼロ
- データの後ろに `DATA_DESCRIPTOR_SIG` と実値

を書きます。

つまり「先に分からない値はゼロで埋め、あとから後置きで書く」という仕組みです。

## CRC32 は何のためにあるか

圧縮や展開が終わったあと、「本当に元データと同じか」を検証するために CRC32 を使います。

この実装では [crc32.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/crc32.py) が CRC32 を計算し、  
[zip_format.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/zip_format.py) の `extract_all` が

- 展開後サイズ
- CRC32

をチェックしています。

CRC は検査値です。
元データや展開結果が壊れていないかを確かめるために使います。

つまり CRC32 で見ているのは、「どれだけ縮んだか」ではなく「展開後の内容が正しいか」です。

## Central Directory が持つもの

中央ディレクトリには、各ファイルについて少なくとも次のような情報があります。

- ファイル名
- 圧縮方式
- CRC32
- 圧縮サイズ
- 非圧縮サイズ
- ローカルヘッダの位置

このリポジトリの `ZipEntryInfo` は、その情報をそのまま表すデータ構造です。

この設計があるので、

- `inspect`
- `unpack`
- `explain-zip`

は、どれも中央ディレクトリを起点に動きます。

## EOCD は「ZIP の最後のしおり」

EOCD の役割は、「中央ディレクトリはどこから始まるか」「何件あるか」を教えることです。[^appnote-eocd]

この実装は `find_eocd_offset` でファイル末尾からさかのぼって EOCD を探します。  
だからまず EOCD を見つけ、それから中央ディレクトリへ行く流れになります。

この流れは、仕様の構造とそのまま対応しています。

## ここから出力とコードで確認する

サンプル ZIP の説明出力をもう一度見ます。

```text
zip_bytes=284
eocd_offset=262
central_directory_offset=143
central_directory_size=119
entry_count=2
comment_length=0
input/a.txt: local_header=0 data=41 method=deflate compressed=10 uncompressed=18 flags=bit3-data-descriptor,utf8-name
input/deep/b.txt: local_header=67 data=113 method=deflate compressed=14 uncompressed=18 flags=bit3-data-descriptor,utf8-name
```

ここから読めること:

- ZIP 全体は 284 バイト
- 143 バイト目から中央ディレクトリ
- 262 バイト目から EOCD
- `input/a.txt` の Local Header は 0 バイト目
- `input/a.txt` の圧縮データは 41 バイト目から
- `input/deep/b.txt` の Local Header は 67 バイト目

ZIP は「何がどこにあるか」を数値でたどる形式だと分かります。

ファイル先頭 160 バイトのダンプです。

```text
00000000: 50 4b 03 04 14 00 08 08 08 00 14 b8 6a 5c 00 00
00000010: 00 00 00 00 00 00 00 00 00 00 0b 00 00 00 69 6e
00000020: 70 75 74 2f 61 2e 74 78 74 4b cc 29 c8 48 54 40
00000030: 22 b9 00 50 4b 07 08 3e 45 97 7c 0a 00 00 00 12
00000040: 00 00 00 50 4b 03 04 14 00 08 08 08 00 14 b8 6a
00000050: 5c 00 00 00 00 00 00 00 00 00 00 00 00 10 00 00
00000060: 00 69 6e 70 75 74 2f 64 65 65 70 2f 62 2e 74 78
00000070: 74 cb c9 cc 4b 35 e4 02 91 46 60 d2 98 0b 00 50
00000080: 4b 07 08 fe 43 4a 3c 0e 00 00 00 12 00 00 00 50
00000090: 4b 01 02 ...
```

ここで見分けるべきシグネチャは次です。

- `50 4b 03 04`
  - Local File Header
- `50 4b 07 08`
  - Data Descriptor
- `50 4b 01 02`
  - Central Directory Header
- `50 4b 05 06`
  - EOCD

このリポジトリの [zip_format.py](https://github.com/sekihan02/zip-edu/blob/master/src/zip_edu/zip_format.py) でも、同じ値をシグネチャとして定義しています。

例えば EOCD の最後の部分は:

```text
50 4b 05 06 00 00 00 00 02 00 02 00 77 00 00 00 8f 00 00 00 00 00
```

ここで

- `77 00 00 00` は `0x00000077 = 119`
- `8f 00 00 00` は `0x0000008f = 143`

です。

つまり中央ディレクトリのサイズは 119、開始オフセットは 143 です。

Data Descriptor を付ける処理はこうです。

```python
local_crc = 0 if use_data_descriptor else crc
local_comp_size = 0 if use_data_descriptor else len(comp_data)
local_uncomp_size = 0 if use_data_descriptor else len(raw_data)
...
if use_data_descriptor:
    zip_data.extend(
        struct.pack(
            "<IIII",
            DATA_DESCRIPTOR_SIG,
            crc,
            len(comp_data),
            len(raw_data),
        )
    )
```

EOCD から中央ディレクトリへたどる処理もこうです。

```python
eocd_offset = find_eocd_offset(data)
(
    _sig,
    _disk_no,
    _cd_disk_no,
    _entries_on_disk,
    entry_count,
    cd_size,
    cd_offset,
    comment_len,
) = struct.unpack_from("<IHHHHIIH", data, eocd_offset)
```

図・出力・コードが同じ構造を指していることが分かります。

## 最後にもう一度答えると

- ZIP は前半にファイル本体、後半に目次を持つ
- EOCD と中央ディレクトリがあるから一覧や位置確認ができる
- CRC32 は展開結果の正しさを確かめるための値である
- Data Descriptor は後置きで実値を書くための仕組みである

次回は、圧縮本体の最初の段階である LZ77 を見ます。

## 参考

[^appnote-container]: PKWARE, "APPNOTE.TXT", sections 4.3.7, 4.3.9, 4.3.12 and 4.3.16. https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT
[^appnote-endian]: PKWARE, "APPNOTE.TXT", section 4.3.3. https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT
[^appnote-bit3]: PKWARE, "APPNOTE.TXT", section 4.4.4 and local header description. https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT
[^appnote-eocd]: PKWARE, "APPNOTE.TXT", section 4.3.16. https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT

---

原文 URL: https://sekihan02.github.io/articles/zip-edu-05-zip-container/

---

<a id="zip-edu-04-bitstream-and-blocks"></a>

## 第4回: ZIP実装を読みながらDeflateのブロックとビット列を理解する

- 公開日: 2026-03-16
- 更新日: 2026-03-16
- カテゴリ: ZIP
- タグ: ZIP, Deflate, ビット列, 圧縮
- 原文サイト: [sekihan02.github.io](https://sekihan02.github.io/articles/zip-edu-04-bitstream-and-blocks/)

> zip-edu の `bitstream.py` と `deflate.py` を読みながら、Deflate のブロック構造と LSB-first のビット列を理解します。

[zip-edu](https://github.com/sekihan02/zip-edu/tree/master) の `bitstream.py` と `deflate.py` の出力を見ながら、Deflate のブロックがどのように並び、ビットがどのような順で詰められるのかを整理します。

## まず連載全体の地図

この連載は、ZIP を次の順で追っていきます。

1. 第1回: ZIP 全体の役割と流れをつかむ
2. 第2回: LZ77 で繰り返しをトークンに変える
3. 第3回: ハフマン符号でトークンを短いビット列にする
4. 第4回: Deflate をブロックとビット列として組み立てる（今回）
5. 第5回: できたデータを ZIP コンテナに入れる
6. 第6回: ここまでの仕組みがリポジトリのどこにあるか整理する
7. 第7回: 基本実装の外側にある拡張仕様を見る

この第4回は、LZ77 とハフマン符号で用意した内容が、最終的に Deflate のブロックとしてどうビット列になるかを見る回です。

## この回で答える問い

- BFINAL と BTYPE は何を表しているのか
- stored / fixed / dynamic の違いは何か
- ビットが LSB-first で書かれるとはどういうことか
- Deflate ブロック全体はどの順で組み立てられるのか

## 先に答えると

- Deflate ブロックの先頭には、最後のブロックかどうかと、ブロック方式が入ります。
- stored は無圧縮、fixed は固定表、dynamic はデータごとに表を送る方式です。
- Deflate のビットは、フィールドによって下位ビットから書かれる部分があります。
- つまり Deflate は、単なる「圧縮済みデータ」ではなく、ブロックの構造を持ったビット列です。

## Deflate ブロック全体の流れ

Deflate の1ブロックは、ざっくり次の順で書かれます。

1. `BFINAL`
   - これが最後のブロックか
2. `BTYPE`
   - stored / fixed / dynamic のどれか
3. 方式ごとの本体
   - stored: 生データ
   - fixed/dynamic: literal/length と distance の列
4. end-of-block

この流れは RFC 1951 の block format と一致します。[^rfc1951-blocks]

## まずはイメージから

Deflate は、圧縮データ全体を1枚の紙として書くのではなく、
「ブロック」という単位で区切って書きます。

- ブロックの種類を最初に書く
- その種類に応じたデータを書く
- 最後なら `BFINAL=1`

という流れです。

```