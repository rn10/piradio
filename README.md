Raspberry Piをradikoラジオにする(らじるも使える)

![sample](https://user-images.githubusercontent.com/49352933/82976092-272b5800-a019-11ea-9ec7-32c22b80a651.jpg)

要るもの:

どこのご家庭にでもある以下のもを使います
1. Raspberri Pi 3とか4とかZero Wとか
2. GPIOに繋ぐスイッチ5個(PUSH ON)
3. フレームバッファ(/dev/fbX)として使える液晶(このスクリプトでは320x240を想定)
   AdafruitのPiTFT mini(2.9")を使っていますが、タッチパネル対応がじゃまくさいの
   でタッチには対応していません。スイッチ操作だけです。
4. 日本語表示用の適当なTTFフォント
5. ffmpegを別途入手してください。ffplayを使用します。

画面にはpygameを使用していますので、pygameで使えるフレームバッファが必要です。
今のところ Python 2.7 用です

Radikoの再生スクリプトはあちこちにありますが、出所が明確ではないものが多いので新
規に書いて、radiko.pyで実装してあります。Radikoプレミアムにも対応しています。エリ
アフリーを使いたい場合にはプレミアムに登録しメールアドレスとパスワードを設定すれ
ば使えます。 プレミアム使用時に頻繁なログインを避けるため cookieの情報をカレント
ディレクトリに持ちますのでスクリプトが書き込めるディレクトリで起動してください。
他の場所にcookieを保存したい場合にはスクリプトを修正してください。

フレームバッファがない場合でもAPI操作のラジオになります。-noguiを付けて起動する
とAPIだけで動きます。が、初期起動時の音量が小さすぎるので vol_val を調整して起動
してください。今のところAPIで音量調整はサポートしていません。


局リストの作り方:

デフォルトのまま使う場合には起動ディレクトリの下に ./stations というディレクトリ
を作り、その 中に station_list という名前でファイルを作成します。
項目は次の通りです

  局の識別子,日本語局名,英数字のみの局名,ロゴ画像ファイル名,再生方式

　例：
　RN1,ラジオ日経第一,RADIO NIKKEI 1,RN1.png,radiko

再生方式は今のところradikoかradiruです。らじるの場合には識別子のところにCDNのURL
を指定します。他の再生方式でもまあ対応できるので対応の方法は中身を見てください。

現在または指定したエリアの局を一括して生成できるpythonスクリプトも同梱しています

python get_stations.py > station_list

と実行すると現在のエリアの局情報を取得し局リストをつくることができます。この時、
各局のロゴファイルも自動的にダウンロードします。
特定エリアの局情報を取得するには

python get_stations.py JP1 

のように実行します。JPxのxは都道府県コードです。

音が出ない場合にはオーディオデバイスの設定を確認してください。FFPlayで使えるデバ
イスが指定されていれば音は出るはずです。


API:

Webブラウザなどから制御可能なように超簡単なAPIも用意してあります。何のセキュリティ
もありませんので家で使う程度にしてください。
piradio_ai_portでAPIが使用するポート番号を指定します。localhost:ポート番号でAPIが
待ち受けます。現在のところRadikoにしか対応していませんが、以下の2種類のメッセージ
を送り付けるだけで再生と停止が行えます。
START:RN1 - Radikoの識別子 RN1 を再生開始します
STOP:     - 現在の再生を停止します。


