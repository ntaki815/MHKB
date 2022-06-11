# MHKB
Keyboard data to create a mechanical keyboard based on HHKB JIS layout

## Overview

HHKBのJIS配列を元にした自作キーボードを作成するための設計データです。

![png](https://github.com/ntaki815/MHKB/blob/main/keyboard-layout/mhkb.png?raw=true)

## Design Steps

### 1. Creating a Keyboard Layout
1. オンラインのキーボードレイアウトサイト（[Keyboard-Layout-Editor.com](http://www.keyboard-layout-editor.com/)）を利用して、HHKBのキー配列に合わせてキーを配置していきます。

2. 配置が完了したら右上のDownloadボタンから、SVG・PNG・JSONファイルをダウンロードします。

### 2. Preparation of PCB
1. 基板設計のためのツール[KiCAD](https://www.kicad.org/)をインストールします。

2. 自動配線のためのツール[Freerouting](https://github.com/freerouting/freerouting)をgithubリポジトリからダウンロードしてインストールします。

3. [yskoht/keyboard-layouterのリポジトリ](https://github.com/yskoht/keyboard-layouter)からPCB設計時にKeyboard-Layout-Editorで作成したレイアウトに沿ってスイッチを自動配置してくれるKiCADのプラグインを取得しておきます。

4. keyboard-layouterで読み込めるようKeyboard-Layout-Editorで作成したレイアウトのキーレジェンドを番号に変えておきます。

5. キーボード作成に使用するパーツのフットプリント等を[foostan/kbdのリポジトリ](https://github.com/foostan/kbd)から取得しておきます。

### 3. Schematic Design
1. KiCADを起動し、新規プロジェクトを作成します。

2. .kicad_schファイルを選択して回路図エディターを開きます。

3. 設定＞シンボルライブラリーを管理 からシンボルライブラリーを開き、[foostan/kbdのリポジトリ](https://github.com/foostan/kbd)から取得したシンボルを追加します。

4. ProMicro、スイッチやダイオードを配置して回路図を書いていきます。（詳細は割愛）
    + ProMicroで使用できるピン数が限られており、本キーボードは69キーあるため、7行×10列で格子状に回路を組みます。
    + 検査＞エレクトリカル ルール チェッカー(ERC) からERCを実行して問題ないことを確認します。

5. ツール＞フットプリントを割り当て から配置したシンボルに対してフットプリントを割り当てていきます。

6. ツール＞回路図から基板を更新 にて作成した回路図の内容を基板に反映します。
    + KiCAD 6.0~からネットリストがなくなり直接PCBエディターのほうに反映できるようになったようです。

### 4. PCB Design
1. .kicad_pcbファイルを選択してPCBエディターを開きます。

2. ツール＞外部プラグイン＞プラグイン ディレクトリーを開く からディレクトリーを開き、keyboard_layouter.pyのファイルを配置します。

3. ツール＞外部プラグイン＞プラグインを更新 を押下してプラグインを読み込みます。

4. ツール＞外部プラグイン＞Keyboard Layouter を押下してプラグインを起動します。

5. Keyboard-Layout-Editorで作成した（Preparation of PCBのフェーズで番号を振った方の）JSONファイルを指定してRunを実行します。

6. ProMicroやReset SW等のパーツを配置してレイアウトを整えます。

7. ファイル＞エクスポート＞Specctra DSN... から.dsnファイルをエクスポートします。

8. Freeroutingを起動し、Open Your Own Designからエクスポートした.dsnファイルを選択します。

9. Autorouterを押下して自動配線を実行します。

10. Completeになったら、File＞Export Specctra Session File を押下して.sesファイルに出力します。

11. KiCADのPCBエディターに戻り、ファイル＞インポート＞Specctra セッション... から.sesファイルをインポートします。

12. 検査＞デザインルール チェッカー からデザインルール チェッカーを実行し、エラー箇所を特定します。

13. Holeに配線が近すぎる等のエラーが大半だと思うので、エラーがなくなるまでマニュアルで修正をしていきます。

14. Edge.cutsのレイヤーを選択し、外線を引いていきます。

15. 塗りつぶしゾーンを追加を選択し、F.Cu・B.CuのGNDに対して塗りつぶしをします。

16. ファイル＞プロット... から製造ファイルを出力します。

### 5. Plate Design
1. オンラインのキーボードレイアウトサイト（[Plate & Case Builder](http://builder.swillkb.com/)）を利用して、SWをはめ込むためのトッププレートを設計します。

2. サイトを開いたらKeyboard-Layout-Editorで作成したレイアウトのRaw dataをコピーして、Plate Layoutに貼り付けます。

3. Switch TypeにMX{_t:3}を選択

4. Case TypeにPoker - 60%を選択

5. Plate CornersをONにしてradiusに2mmを指定

6. Draw My CAD!!! を押下

7. CAD Outputのタブを開き、svg・dxfファイルをダウンロードします。
