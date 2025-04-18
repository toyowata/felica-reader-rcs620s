# felica-reader-rcs620s

<img width="600" src="https://dl.dropboxusercontent.com/s/kfclvg1qqregvnt/IMG_9299.png">

FeliCa リーダー・ライターを使用したSuica/PASMO履歴リーダです。

USBケーブルでPaspberry Pi picoとパソコンと接続し、履歴データを表示させることができます。I2C接続LCDを付ければ、現在の残高を表示します。残高表示は、以下のFelica対応ICカードに対応しています。

* Suica
    * ICOCA
    * TOICA
    * Kitaca
    * はやかけん
    * AOPASS
    * iGUCA
    * Iwate Green Pass
    * odeca
    * SUGOCA
    * cherica
    * totra
    * manaca
    * SAPICA
    * Welcome Suica
* PASMO
* Edy
* nanaco
* won
* nimoca
* ecomayca
* PiTaPa
* PASPY

また、AS-289R2プリンタシールドを接続すると、Suica/PASMO履歴データを印字することができます。

# 使用した機材

* Paspberry Pi pico  
https://www.switch-science.com/catalog/6900/
* FeliCa リーダー・ライター RC-S620S  
https://www.switch-science.com/catalog/353/
* FeliCa RC-S620S/RC-S730 ピッチ変換基板のセット(フラットケーブル付き)  
https://www.switch-science.com/catalog/1029/
* I2C接続の小型LCD搭載ボード(3.3V版)  
https://www.switch-science.com/catalog/1405/
* AS-289R2プリンタシールド  
https://www.switch-science.com/catalog/2553/

# デバイスとの接続

Pasberry Pi picoと他の部品は以下のように接続してください。

|Rasberry Pi pico|RC-S620/S|I2C LCD|AS-289R2|
|---|---|---|---|
|GPIO4 (pin 6)|||RxD1 (D1)|
|VSYS (pin 39)|||5V|
|3V3(OUT) (pin 36)|VDD (pin 1)|VDD (pin 2)||
|GND (pin 38)|GND (pin 4, 6)|GND (pin 3)|GND|
|GPIO16 (pin 21)|RXD (pin 2)|||
|GPIO17 (pin 22)|TXD (pin 3)|||
|GPIO14 (pin 19)||SDA (pin 4)||
|GPIO15 (pin 20)||SCL (pin 5)||

# 必要なツールのインストール

## ツールチェインのインストール
以下の情報を参照して、ビルドに使用するツールチェインをインストールしてください。
https://github.com/mbed-ce/mbed-os/wiki/Toolchain-Setup-Guide

## pico-sdk
以下のサイトを参考にしてください。  
https://github.com/raspberrypi/pico-sdk

## picotool
以下のサイトを参考にしてください。  
https://github.com/raspberrypi/picotool

# プログラムのビルドと書き込み

## コマンドライン（cmake）でビルドする
以下のコマンドでリポジトリをクローンして、ビルドします。

```
$ git clone --recursive https://github.com/toyowata/felica-reader-rcs620s
$ cd felica-reader-rcs620s
$ mkdir build && cd build
$ cmake .. -GNinja -DMBED_TARGET=TINY2040
$ ninja
```

## Raspberry-PI pico に書き込む
BOOTSELモードに設定し（基板上のボタンを押したまま電源を入れる）、以下のコマンドを実行します。

```
$ ninja flash-felica-reader-rcs620s
```

## プログラムの実行

プログラム書き込み後、自動的にリセットされプログラムが起動します。  
リセット後、3秒以内にBOOTスイッチを押すと、USBシリアルターミナルと接続するモードに移行します。
TeraTerm, CoolTerm等のシリアルターミナルソフトウェアでパソコンと接続します（115200,8,N,1）。日本語を表示するので、UTF8が表示できるモードに設定してください。  
FeliCa リーダー・ライター上にSuicaを乗せると、履歴情報が表示されます。

履歴情報の例

```

*** RCS620S テストプログラム ***

12 07 00 00 28 2B 1D 1E 00 00 1C 25 00 00 01 00 
機種種別: 券売機
利用種別: 新規
横浜線 町田駅
処理日付: 2020/01/11
残額: 9500円

12 03 00 00 28 2B 1D 1E 00 00 90 24 00 00 02 00 
機種種別: 券売機
利用種別: きっぷ購入
横浜線 町田駅
処理日付: 2020/01/11
残額: 9360円

C7 46 00 00 28 2B A6 80 40 72 6D 23 00 00 03 00 
機種種別: 物販端末
利用種別: 物販
処理日付: 2020/01/11 20:52:00
残額: 9069円

17 01 00 25 28 2C 01 23 FF 1E 5C 0F 00 00 06 00 
機種種別: 簡易改札機
利用種別: 自動改札出場
入出場種別: 券面外乗降
東海道本線 小田原駅 - 伊豆急行線 伊豆急下田駅
処理日付: 2020/01/12
残額: 3932円

C7 46 00 00 28 2C 5C 8A BC 67 A8 0B 00 00 07 00 
機種種別: 物販端末
利用種別: 物販
処理日付: 2020/01/12 11:36:10
残額: 2984円
```

## 注意点と既知の問題

### リセット方法
リセット用のタクトスイッチを付けると便利です。  
https://nuneno.cocolog-nifty.com/blog/2021/03/post-c5ccb6.html

### Mbed OSのバージョン
現在このプログラムで使用しているMbed OSは、Mbed CE (Community Edition) 版を使用しています。
https://github.com/mbed-ce/mbed-os

### パソコンとのUSBシリアル接続
リセット後、3秒以内にBOOTスイッチを押すと、USBシリアルターミナルと接続するモードに移行します。
このモードではパソコンのUSBシリアルターミナルと接続しないと、プログラムがスタートしないように設定しています（ブロックモード）。シリアルターミナルと接続しない場合は、リセット後に3秒間放置するか、`main.cpp`の以下のコードを変更してください。

変更前
```
USBSerial serial(true);
```
変更後
```
USBSerial serial(false);
```

### サイバネコード
駅データのサイバネコードは、以下のサイトのデータを使用させていただきました。  
https://github.com/MasanoriYONO/StationCode
https://ja.ysrl.org/atc/station-code.html

このデータから必要な項目だけを抽出し、csv形式からバイナリ形式に変更を行っています。変換用のツールは以下に公開しました。  
https://github.com/toyowata/csv2bin

### 制約事項
* Mbed CLIまたは、CLI2 でのビルドはサポートしていません
* オリジナルのMbed OSを使用したい場合は、[こちら](https://github.com/toyowata/suica-reader-rcs620s)のリポジトリのコードを使用してください
* 誤動作を防ぐために、同じカードを連続して読み込むことはできません。同じカードを読み込む場合は、リセットを行ってください。
