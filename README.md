# 4d-tips-data-analyzer
ストラクチャ・データ・インデックスファイルをバイナリレベルで解析するツール

<img width="954" alt="2017-08-22 7 12 09" src="https://user-images.githubusercontent.com/10509075/29540459-6ce8b26a-8709-11e7-9e98-e7833f768727.png">
<img width="954" alt="2017-08-22 7 11 41" src="https://user-images.githubusercontent.com/10509075/29540464-6cede4ce-8709-11e7-8a89-78153b0b4410.png">
<img width="954" alt="2017-08-22 7 12 33" src="https://user-images.githubusercontent.com/10509075/29540460-6cea2ff0-8709-11e7-97ff-ee01fb2fe9a2.png">
<img width="954" alt="2017-08-22 7 12 40" src="https://user-images.githubusercontent.com/10509075/29540461-6ceaccbc-8709-11e7-9009-34fe0e268923.png">
<img width="954" alt="2017-08-22 7 12 43" src="https://user-images.githubusercontent.com/10509075/29540463-6ceca122-8709-11e7-89f2-300a4cda88c8.png">
<img width="954" alt="2017-08-22 7 12 17" src="https://user-images.githubusercontent.com/10509075/29540466-6d0bea6e-8709-11e7-9436-78ee129ab233.png">
<img width="954" alt="2017-08-22 7 12 25" src="https://user-images.githubusercontent.com/10509075/29540465-6d0a1d74-8709-11e7-8595-93d1435d4abf.png">
<img width="955" alt="2017-08-23 12 06 14" src="https://user-images.githubusercontent.com/10509075/29597112-807de604-87fb-11e7-8cbe-0c4eeb1af480.png">
<img width="950" alt="2017-08-23 12 05 06" src="https://user-images.githubusercontent.com/10509075/29597113-808413f8-87fb-11e7-9ee0-3f3d079ffd34.png">
<img width="950" alt="2017-08-23 12 05 08" src="https://user-images.githubusercontent.com/10509075/29597111-807d9938-87fb-11e7-9747-e14d7d0ada32.png">

---

データファイル・ストラクチャファイル・インデックスファイルなど，``DB4D``が使用する広義のデータファイルは，どれも同じ構造をしています。このツールは，それらのファイルをバイナリモードで読み込み，解析するために開発されました。

**注記**: オリジナル版は，[2015年のテクニカルノート](http://kb.4d.com/assetid=77253)（Jean-Pierre Ribreau 著）として一般向けに公開されています。

### ポイント

* MSCとは違い，レコードをバイナリレベルで読み込むので，チェックサムやタイムスタンプの異常や，マッピングされていない領域の検出などを解析することができます。

* ブロックレベルでファイルの構造を分析し，マップ画像で確認することができるので，データファイルの断片化や余分な節約できるはずのディスクサイズなどを解析することができます。

**注記**: バイナリモードでファイル全体を解析するので，おおきなファイルの処理には数分・数時間・数日を要するかもしれません。ヘッダーとアドレステーブルは，全体の読み込みが完了する前であっても，解析することができます。いずれにしても，コンパイルモードで実行することが勧められています。

### 使い方

ファイルメニューより「データ・ストラクチャ・インデックスファイルを解析…」を選択します。

「ファイルを開く…」ボタンをクリックし，データファイル・ストラクチャファイル・インデックスファイルのいずれかを開きます。

### ファイル

「一般」ページには，解析の進捗および基本的なレポートが出力されます。

「解析」ページには，各ステップの開始・終了時刻および解析に要した時間が出力されます。

「異常」ページには，エラーメッセージが出力されます。

* ポイント

**最後のオペレーション**: これが``1``でない場合，キャッシュからの書き込みが正常に完了していない恐れがあります。

**データセグメントの数**: v11以降，セグメント数は常に``1``です。

**4Dバージョンコード**: ``DB4D``の内部バージョンコードであり，いわゆるバージョン名とは違います。

**論理EOF**: ファイルの物理的なサイズよりも手前に位置するのが普通です。この位置以降に書かれたデータに意味はありません。

**セグメントサイズの上限**: v11以降，使用されていません。

**インデックスにリンクする乱数**: データファイル（``.4DD``）とデータインデックス（``.4DIndx``），ストラクチャファイル（``.4DB``）とストラクチャインデックス（``.4DIndy``）のペアリングに使用されるUUIDのことです。

**ログに記録された最後のアクション**: ログファイルを使用していなければ，この値が``0``になります。

**アドレステーブルで最初の削除の痕跡**: テーブルを削除したことがなければ「削除の痕跡なし」となります。

### マップ

ブロックアロケーションマップあるいはブロックマップをビットマップ画像として表示します。

**ブロックとは**:  ``DB4D``が使用する広義のデータファイルは，どれも``128``バイトのブロックで構成されています。これは，``HDD``などのデバイスが使用する物理的なブロックサイズには左右されません。ブロック数の上限は``65,536 * 8,192 * 16,384``個と決められています。ですから，ファイルの論理的な最大サイズは``1,048,576``ギガバイト（``1``ペタバイトまたは``1,024``テラバイト）です。

ファイルは，下記の基本領域で成り立っています。

* ヘッダー
* ブロックアロケーションテーブル
* レコードアドレステーブルのツリー
* BLOBアドレステーブルのツリー
* レコード（BLOB）

**ヘッダー**: 1次アドレステーブルのアドレスや，ファイルの基本情報（``UUID``など）が書き込まれています。ヘッダーのサイズは固定であり，``2``ブロック，つまり``256``バイトです。ヘッダーの後には，ブロックアロケーションテーブルが続きます。

**ブロックアロケーションテーブル**: レコードが削除されたり，更新されたりすると，ファイル内に未使用のブロックが次第に増えてゆきます。現在どのブロックが使用されているのか，という情報は，ブロックアロケーションビットマップ（``1``ブロックにつき``1``ビット）で管理されています。それらのビットマップの場所を記録したものがブロックアロケーションテーブルです。このテーブルには，``4,096``件のエントリーがあり，それぞれのエントリーは``1``個のブロックアロケーションビットマップが存在する場所を指しています。

**レコードアドレステーブル**: データテーブル（いわゆるテーブル）が作成されると同時に作成されるテーブルです。それぞれのテーブルにつき1次アドレステーブルが作成され，それがいっぱいになると，2次・3次テーブルが追加されてゆきます。どのアドレステーブルも構造は一緒で，``1,024``個のエントリーがあります。ですから，レコード数が``1,024``件に満たないテーブルは，1次アドレステーブルだけで十分です。

1次アドレステーブルがいっぱいになると，新しいアドレステーブルが追加され，それが1次アドレステーブルとなります。それまで1次アドレステーブルだったもの（レコードのアドレスが記録されている）は，2次アドレステーブルとなり，新しい1次アドレステーブルの最初のエントリーは，このアドレステーブルを指します。2次アドレステーブルを使用すれば，``1,024 * 1,024``件まで，3次アドレステーブルを使用すれば，``1,024 * 1,024 * 1,024``件のアドレスを管理することができます。およそ``10``億件というレコード数の上限は，この3段階構造に由来しています。

レコードアドレステーブルには，``128``バイトのヘッダー情報に続けて``1,024``個のアドレス情報が記録されています。アドレス情報は，レコードあるいは別のレコードアドレステーブルの場所（``8``バイト），さらに，レコードであればサイズ（``4``バイト）をで構成されています。レコードアドレステーブル``1``個は，ちょうど``97``ブロック（``12,288``バイト）に収まります。

レコードアドレステーブルに記録されているアドレスは，対象であるレコードあるいはレコードアドレステーブルの最初のブロックの場所（ファイル先頭からのオフセット値）を指しています。一般に「レコード番号」と呼ばれるものは，レコードアドレステーブル内におけるエントリー番号のことです。有効なレコード番号の上限値は，レコードの総数ではなく，アドレステーブルの数によって決まります。

**レコード（BLOB）**: レコードは，``32``バイトのヘッダー部，データ部，およびレコードマップで構成されています。レコードマップは，マイクロストラクチャとも呼ばれます。レコードマップは，このレコード``1``件の構造を説明したタグ情報であり，各フィールドのデータ型や，データ部内における位置（オフセット値）が記録されています。タグ（マイクロストラクチャ）がレコード毎に存在するおかげで，フィールドタイプがストラクチャ側で変更されたとしても，オリジナルの値を読み取ることができます。

マップは，``128``バイトのブロック``1``個を``1``ドットで表現したものです。最高``64``倍までズームすることができます。

対象サイズがおおきい場合，マップは複数のページに分割されます。デフォルトの設定では，``128``メガバイト毎にページが追加されるようになっています。

ブロックアロケーションマップの場合，内容が``4``色で表現されます。

* 空きブロック（``Empty Block``）: 使用されていないブロックです（白）。

* 予約ブロック（``Claimed Block``）: 使用されているブロックです（グレー）。

* マッピングされているが使用されていない（``Recoverable``）: ブロックアロケーションマップによれば，予約されていることになっていますが，解析の結果，実際には使用されていないことが判明したブロックです（ブルー）。

* 使用されているがマッピングされていない: 常に赤で表示されます。ブロックアロケーションマップによれば，空きブロックですが，解析の結果，実際には使用されていることが判明したブロックです。これはファイルが破損していることを示唆するものであり，危険な状況です。ブロックを利用とすれば，データが壊れてしまうからです。ファイルの圧縮または修復が必要です（レッド）。

歯車のアイコンをクリックして「タグによる修復で回復できるブロックを表示」オプションを有効にすると，回収できるブロックがブルーグレー色で表示されます。アロケーションマップによれば，使用されていない，つまり空きブロックですが，内容的には有効なオブジェクトが収まっています。たとえば，レコードを削除した場合などにこれが発生します。タグによる修復でそのようなレコードの「削除を取り消す」ことにより，削除されたデータを回復できることがあります。

ブロックマップの場合，各ブロックの内容に応じて色が付けられます。たとえば，レコードは赤，BLOBは紫で表示されます。各ブロックに割り当てられる色は，設定画面で変更することができます。レコードなどのオブジェクトは，大抵の場合，複数のブロックに分散しています。有効なタグ情報（ヘッダー）が含まれるブロックは，そのオブジェクトの第``1``ブロックであると判断できます。このヘッダーには，オブジェクトのサイズ情報も含まれているので，後続のブロック何個が同じオブジェクトに使用されているか，計算することができます。

マップ上でマウスポインターを移動すると，なぞったブロックの位置情報が画面に表示されます。``0.5``秒ほどマウスポインターを停止すると，さらに詳しい情報が表示されます。この情報の読み方は以下のとおりです。

```
ブロック番号 3 846 [0x0F06]（選択するにはクリック）
タイプ = rec1 (Record) 4 629 バイト（37 ブロック）
タイムスタンプ = 0xBCD2 E101 0A08 E107 チェックサム = FFFF B694 8A4D
レコード = 28 テーブル = 2 フィールド = 20
```

ブロック番号は``3,846``です。オブジェクトタイプ``rec1``つまりレコードのヘッダーブロックであり，全部で``37``個のブロックを占有しています。したがってオブジェクトのサイズは``4.629`` バイトです。これはテーブル番号``2``のレコード番号``28``であり，中には``20``個のフィールドが収められています。

歯車のアイコンをクリックして「テーブル番号を指定」オプションを有効にすると，特定のテーブルに関連のあるブロック（レコードやBLOB）だけに色が付けられます。「ブロックアロケーションテーブルで予約されたブロックを表示（除外）」オプションは，アロケーションマップで予約されているブロックだけを表示して「実際には使用されていないブロックを隠す」か，逆にそのようなブロックを際立たせることができます。「タグによる修復で回復できるブロックを表示」オプションを有効にすると，内容的には有効なオブジェクトが記録されている空きブロックを際立たせることができます。たとえば，「ブロックアロケーションテーブルで予約されたブロックを除外」オプションと併用することにより，タグによる修復で回復できるブロックの場所を特定することができます。

ブロック上で右クリックし，コンテキストメニューから「16進数解析」を選択することにより，選択したブロックや隣接するブロックの中身を16進数ビューで閲覧することができます。あるいは，虫眼鏡のアイコンをクリックするか，コンテキストメニューから「詳細を表示…」することにより，なぞったブロックの情報と中身をパレットウィンドウで閲覧することができます。「選択ブロック」には，最後にクリックしたブロックの番号が表示されます。

### 16進数ビュー

ファイルの中身を典型的な16進数ビューで閲覧することができます。

**相対アドレス**: チェックされている場合，アドレスはブロック先頭からのオフセット値となります。チェックされていなければ，ファイル先頭からのオフセット位置です。

**ASCIIを表示**: 16進数を文字として表示します。左右のビューは，選択の範囲が連動するようになっています。テキストはUTF-16，また整数はリトルエンディアンで記録されているため，時折，読み替えて解釈する必要があります。たとえば，``1cer``という整数のタグ情報は，``rec1``つまり，レコードという意味のタグです。

**移動**: 指定したブロックにジャンプすることができます。左側の入力エリアにアドレスを入力してから「アドレスに移動」ボタンをクリックするか，右側の入力エリアにブロック番号を入力してから「ブロックに移動」ボタンをクリックしてください。左右移動ボタンで隣接するブロックに移動することもできます。1度に読み込むブロック数は「読み込みブロック数」で指定することができます。「16進数」チェックボックスが有効にされている場合，アドレスおよびブロック番号は16進数で表示されます。16進数でアドレスを入力する場合，「反転」ボタンをクリックすることにより，値をリトルエンディアンからビッグエンディアンに変換することができます。ファイルに書き込まれているデータはリトルエンディアンですが，アドレスはビッグエンディアンで参照するのが一般的だからです。

**選択範囲**: 選択した範囲の簡単な要約が表示されます。バイト数により，自動的なASCII変換が実行されます。範囲の長さが``1``バイトであれば，バイト値が16進数・10進数・文字として表示されます。``2``バイトであれば，整数値が16進数・10進数・文字列で表示されます。``3-4``バイトであれば，倍長整数値が整数値が16進数・10進数・文字列で表示されます。``8``バイトであれば，64ビット整数値が16進数・10進数・文字列で表示されます。それよりも長い範囲を選択した場合，範囲だけが表示されます。数値（8バイトまで）は，自動的にビッグエンディアンに変換されて表示されます。
