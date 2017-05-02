px68k-libretro 

forked from https://github.com/hissorii/px68k
backported c68k core from https://github.com/kenyahiro/px68k/ 
(fork of https://github.com/hissorii/px68k using recent c68k yabause core to support X64 build)

All credit goes to hissorii and kenyahiro.

How to use :

First create a 'keropi' folder in retroarch system directory.
put the x68000 bios files in.

Then launch emulator with some content (dim|zip|img|d88|88d|hdm|dup|2hd|xdf|hdf)
F12 to enter menu.


You can launch content with:

- retroarch -L px68k_libretro.so ./content.hdf

- retroarch -L px68k_libretro.so ./content.xdf

- retroarch -L px68k_libretro.so ./content.cmd
 (cmdfile is a text file contening cmd like "px68k /somewhere/software/x68000/content1.dim /somewhere/software/x68000/content2.dim")

- retroarch -L sdlpx68k_libretro.so "px68k /somewhere/software/x68000/content1.dim /somewhere/software/x68000/content2.dim"

- load retroarch , then load core and content from RA menu.

_________________________________________________________________________________________________________________________________________

ポータブルX68000エミュレータ
PX68K ( Portable (x)keropi PRO-68K )
                                                                     2014/07/09

いろいろなデバイスで X68000 エミュレータが動作したら楽しいな、
と思い立ち、この PX68K を作りはじめました。

「Portable」とは「移植しやすいよ」という意味です。
タブレットや PSP 等のポータブルデバイスで動いたらいいな、
という意味もこめています。


PX68K は以下の成分からできています。

  けんじょさん (http://retropc.net/kenjo/) が
  作成された SHARP X68000エミュレータ WinX68k (通称けろぴー)

  NONAKA Kimihiro さん(http://www.asahi-net.or.jp/~aw9k-nnk/keropi/) が
  けろぴーを UNIX/X11 環境で動作する様に移植した xkeropi

  Stephane Dallongeville さんが開発したものを、
  NJ 氏が PSP 用 CPS1/CPS2/MVSP/NCDZ に組み込んだ MC68000 MPU エミュレータ

  cisc 氏(cisc@retropc.net) が作成された FM/PSG 音源実装 fmgen
  (詳細は ソースリポジトリの fmgen/readme.txt を参照)
  PX68K への実装にあたり、以下の変更を行っています
  ・Sample 型を int32 から int16 に変更
  ・PSP 用に 11k, 22k から 44k データ作成ロジックを追加


0. 注意事項

  0.1 全機種共通

  ・マウス等、まだ未対応です。

  0.2 Android 版について

  ・Android 3.1 以上でないと動きません。

  0.3 iOS (iPhone / iPad / iPod touch) 版について

  ・Jailbreak(脱獄)したデバイスが必要です。

  ・iOS 6.1.3 / iPod touch 4G で動作確認しています。
    iOS5 以前では多分動きません。
    iOS 7 は Jailbreak 待ち。

  ・現 iOS 版では、デバイスがスリープすると、px68k を終了します。
    今後改善予定ですが、デバイスの設定で自動ロックを延長または
    しないようにする等してください。

  0.4 PSP 版について

  ・CFW を導入していないと動きません。

  ・現在、PSPの終了処理に10秒程度かかります。
    fopen()/fclose()のコストが高いようなので今後見直します。

  0.5 Raspberry Pi (Raspbian) について

  ・自前でコンパイルしてください。
  ・コンパイルについては develop.txt を参照願います。
  ・その他については下記 Unix 版の章を参照してください。

  0.6 Mac OS X 版について

  ・とりあえず自前でコンパイルしてください。
  ・コンパイルについては develop.txt を参照願います。
  ・メニューを開くF12キーが、既に別のショートカットに割り当てられている
    場合は、システム環境設定の「キーボード」の「ショートカット」で解除して
    おいてください。
  ・その他については下記 Unix 版の章を参照してください。


1. 下準備

  ・BIOS ROM ファイルを用意してください。
    ファイル名は以下のいずれかで、小文字でも大文字でも構いません。
    (iOS 版の場合は、小文字にしてください)

        iplrom.dat, iplrom30.dat, iplromco.dat, iplromxv.dat

    ※特に理由がなければ、iplrom30.datの使用はお勧めしません。
      PSP で起動できない、HD イメージが読み込めない等の不具合報告があります。

  ・フォントファイルを用意してください。
    ファイル名は以下のいずれかで、小文字でも大文字でも構いません。
    (iOS 版の場合は、小文字にしてください)

        cgrom.dat, cgrom.tmp

    フォントファイルがない場合は PC 上で WinX68k 高速版を実行すると、
    cgrom.tmp が生成されますので、それを使ってください。

  ・Human68K は立ち上がるけれど、ゲームが起動できない場合は
    標準のメモリ 1MB では動作しないゲームである可能性が高いです。

    HUMAN68K を起動後、キーボードまたはソフトウェアキーボードで
    「switch」と入力後リターンキーを押し、SWITCH.X コマンドを起動して
    メモリ設定してください。

    PSP 版については、現在エミュレータ側でメモリ上限を 4MB にしていますので、
    それより大きな値を SWITCH.X で設定しないでください。


2. Android 版 / iOS 版について

  2.1 ファイルの配置について

  ・Android 版の場合は、/sdcard/px68k/ ディレクトリを作成し、
    BIOS ROM, フォントファイルを置きます。
    機種によっては、/sdcard ではなく、/sdcard0 だったりするかもしれません。
    その機種の内部ストレージっぽいところに px68k ディレクトリを
    作成してください。

  ・Android で、px68k ディレクトリを外部 SD カードに置きたい等、ディレクトリを
    自分で指定したい場合は、まず以下のディレクトリを探してください。
    内部ストレージは、/sdcard とかそういうところです。

        (内部ストレージ)/Android/data/conm.fc2.blog45.hissorii/files

    見つかったらそこに、dir.txtというファイルを作成し、ファイルの一行目に
    指定したいディレクトリを記述してください。例えば以下の様に。
        /mnt/sdcard/external_sd/px68k

    dir.txt を使う場合は、内部ストレージの px68k ディレクトリは
    削除しておいてください。内部ストレージ優先で読んでしまいます。

    px68k ディレクトリを移動した場合は、config ファイルを消した方がよいです。
    config に /sdcard/px68k/hoge.xdf とか古いイメージファイルの記述が
    残っていて、ディレクトリを移動後にアクセスしに行くと、
    多分 px68k がクラッシュします。

  ・iOS 版の場合は /var/mobile/px68k/ ディレクトリが作成されているので、
    そこに BIOS ROM, フォントファイルを置きます。

  ・エミュレータを開始してもすぐに終了してしまう場合は、これらのファイルの
    置き場所、ファイル名について見直してみてください。

  2.2 イメージファイルについて

  ・Android 版は /sdcard/px68k 、iOS 版は /var/mobile/px68k ディレクトリに
    イメージファイルを置いて下さい。
    サブディレクトリを作成し、その中にファイルを置くこともできます。

  ・読み込み対象となるファイルの拡張子は以下となります。

    .D88 .88D .HDM .DUP .2HD .DIM .XDF .IMG (FD イメージ)
    .HDF (HD イメージ)

  ・ ディレクトリを深く掘りすぎたり、長いファイル名の場合、UI 画面から
     はみ出すのは仕様です。

  2.3 メニュー UI

  ・イメージファイルの選択や各種設定には、メニュー UI を使用します。

  ・Android 版の場合、UI に入る、または抜けるには「メニューキー」を
    押すか、次項の白色ボタンを使用してください。

  ・右上隅の白色ボタンを長押し(0.3秒位)すると UI メニューに入ります。
    UI メニューから出るときにはもう一度白色ボタンを押してください。
    出るときは長押しは不要です。

  ・値の設定には「トリガー1ボタン」を使用します。
  ・値の解除には「トリガー2ボタン」を使用します。

  ・メニュー UI で設定した内容は、UI にて [SYSTEM]->[QUIT] を選択し、
    エミュレータを終了させたタイミングで保存されます。
    Android 版の場合は、バックキー(戻るキー)] を 2 回タップして
    エミュレータを終了させた場合も保存されます。    

    現 iOS 版は、デバイスがスリープ状態になったり、ホームボタンを押すと、
    px68k は設定を保存せずに終了します。
    今後、改善予定です。

  ・設定を初期値に戻す場合は Android 版は /sdcard/px68k/config 、
    iOS 版は /var/mobile/px68k/config ファイルを削除します。

  ・今のところ日本語ファイル名 (UTF-8) の表示が正しくできません。

  ・各設定の詳細は、「5. メニュー UI について」を参照してください。

  2.4 仮想ジョイスティック

  ・画面左側の D-pad っぽいのでスティック移動、画面右側のボタンっぽいのが
    トリガー1, 2に対応します。

  2.5 物理パッド

  ・現在は Android 版のみサポートです。
    iOS 版は SDL が物理パッド対応次第、実装予定

  ・PX68K を起動する前に、物理パッドを接続しておいてください。

  ・物理パッドが有効な場合、エミュレーション中の仮想ジョイスティックは
    表示されなくなります。
    メニューUI / ソフトウェアキーボード操作時は表示されます。

  ・物理パッドは X68000 のジョイスティック操作のほか、メニュー UI や
    ソフトウェアキーボード操作にも使用できます。

  ・ボタンの変更をしたい、使用するアナログパッドやデジタルパッドを指定したい
    場合は、メニュー UI の [HwJoy Setting] にて設定してください。

  2.6 キーボード

  ・画面右上隅の黄色ボタンを長押し(0.3秒位)するとソフトウェアキーボードが
    出現します。

  ・キーボードを指でなぞると、移動できます。

  ・キーボードを画面の右側に追いやるとソフトウェアキーボードが消えます。

  ・緑色の仮想キーを使って、キーボード上のキーを選択します。
    選択されたキーは黒色で表示されます。

  ・緑色の仮想ボタン(TRG1)を押すと、選択されたキーが入力されます。

  ・緑色の仮想ボタン(TRG2)を押すと、BS(Back Space)キーが入力されます。

  2.7 仮想マウス

  ・メニューで[Mouse]を選択すると仮想マウスが有効になります。

  ・仮想マウスと仮想パッドは排他です。

  ・画面全体がタッチパッドになるイメージでマウスカーソルを操作します。

  ・緑色のボタンが、マウスボタンになります。

  2.8 サウンド出力

  ・サンプリング周波数は 22050Hz 固定です。
    今のところ config に書いた値は反映されません。UI 実装時に考えます。
    現在 ADPCM と OPM のみ対応済みで、マーキュリーユニット、MIDI については
    未対応です。

  2.9 エミュレータの終了

  ・メニュー UI から終了してください。
  ・Android 版では、バックキー(戻るキー)を 2 回タップしても終了できます。
  ・現 iOS 版ではホームボタンで終了しますが、これはスリープに変更予定です。


3. PSP 版について

  3.1 ファイルの配置について

  ・EBOOT.PBP と同じディレクトリに .keropi というディレクトリを作成し、
    BIOS ROM, フォントファイルを置きます。

    エミュレータを開始してもすぐに終了してしまう場合は、これらのファイルの
    置き場所、ファイル名について見直してみてください。

  3.2 イメージファイルについて

  ・EBOOT.PBP と同じ ディレクトリにイメージファイルを置いて下さい。
    サブディレクトリを作成し、その中にファイルを置くこともできます。

  ・読み込み対象となるファイルの拡張子は以下となります。

    .D88 .88D .HDM .DUP .2HD .DIM .XDF .IMG (FD イメージ)
    .HDF (HD イメージ)

  ・ ディレクトリを深く掘りすぎたり、長いファイル名の場合、UI 画面から
     はみ出すのは仕様です。

  3.3 メニュー UI

  ・イメージファイルの選択や各種設定には、メニュー UI を使用します。

  ・UI に入る、または抜けるには [START] ボタンを押してください。
  ・値の設定には [○] ボタンを使用します。
  ・値の解除には [×] ボタンを使用します。

  ・メニュー UI で設定した内容は、UI にて [SYSTEM]->[QUIT] を選択するか
    [HOME]ボタンを押してエミュレータを終了させたタイミングで保存されます。

  ・PSP版では、VKey/VBtn 関連の設定は反映されません。

  ・設定を初期値に戻す場合は .keropi/config ファイルを削除します。

  ・各設定の詳細は、「5. メニュー UI について」を参照してください。

  3.4 ジョイスティック

  ・D-pad でスティック移動、○ボタン、×ボタンがトリガー1, 2に対応します。

  3.5 キーボード

  ・キーボードの on/off は [SELECT] ボタンで切り替えます。

  ・キーボード自体の移動はアナログパッドで行います。

  ・D-pad を使って、キーボード上のキーを選択します。
    選択されたキーは黒色で表示されます。

  ・[○]ボタンを押すと、選択されたキーが入力されます。

  ・[×]ボタンを押すと、BS(Back Space)キーが入力されます。

  3.6 マウス

  ・メニューで[Mouse]を選択するとマウスが有効になります。

  ・マウスとジョイスティックは排他です。

  ・アナログパッドはおおまかな動き、デジタルパッドは細かな動きが得意です。

  ・○ボタンが左クリック、×ボタンが右クリックになります。

  3.7 サウンド出力

  ・PSP は今のところサンプリング周波数が 11025Hz 固定です。

  3.8 エミュレータの終了

  ・[HOME] ボタンを押すか、メニュー UI から終了してください。
    電源ぶちっ、だと config や SRAM の保存がされません。


4. Unix 版について

  4.1 ファイルの配置について

  ・~/.keropi に BIOS ROM、フォントファイルを置いてください。

  4.2 イメージファイルについて

  ・実行ファイル px68k と同じ ディレクトリにイメージファイルを置いて下さい。
    サブディレクトリを作成し、その中にファイルを置くこともできます。

  ・読み込み対象となるファイルの拡張子は以下となります。

    .D88 .88D .HDM .DUP .2HD .DIM .XDF .IMG (FD イメージ)
    .HDF (HD イメージ)

  ・ ディレクトリを深く掘りすぎたり、長いファイル名の場合、UI 画面から
     はみ出すのは仕様です。

  ・以下の様に引数で指定することもできます。最初が FDD0, 次が FDD1 。

    $ ./px68k hoge.xdf hogege.xdf

  4.3 メニュー UI

  ・イメージファイルの選択や各種設定には、メニュー UI を使用します。

  ・UI に入る、または抜けるには [F12] キーを押してください。
  ・値の設定には [リターン] キーを使用します。
  ・値の解除には [ESC] キーを使用します。

  ・メニュー UI で設定した内容は、UI にて [SYSTEM]->[QUIT] を選択して
    エミュレータを終了させたタイミングで保存されます。

  ・Unix 版では、VKey/VBtn 関連の設定は反映されません。

  ・設定を初期値に戻す場合は ~/.keropi/config ファイルを削除します。

  ・Unix 版は今のところ日本語ファイル名 (UTF-8) の表示が正しくできません。

  ・各設定の詳細は、「5. メニュー UI について」を参照してください。

  4.4 ジョイスティック

  ・JoyKey モードを有効にすると、キーボードのカーソルキーでスティック移動、
    z キー、x キーがそれぞれトリガー 1, 2 に対応します。
    現在UIがないため、~/.keropi/config をエディタで修正する必要があります。

  4.5 キーボード

  ・PC の 10 キー部の NUMLOCK は X68000 では 10キーの CLR に対応します。
  ・PC の END は X68000 では UNDO に対応します。

  ・X68000 の COPY, かな, ローマ字, コード入力, CAPS, 記号入力, 登録,
    HELP, ひらがな, XF1, XF2, XF3, XF4, XF5, 全角 キーについては
    現在サポートしていません。

  4.6 サウンド出力

  ・サンプリング周波数は 22050Hz 固定です。
    今のところ config に書いた値は反映されません。UI 実装時に考えます。
    現在 ADPCM と OPM のみ対応済みで、マーキュリーユニット、MIDI については
    未対応です。


5. メニュー UI について

  ・メニューの階層と、それぞれの内容を以下に示します。
    設定キー、解除キーについては、各装置向けの説明を参照してください。  

  [SYSTEM]-+-[RESET]
           |   エミュレータをリセットします。
           |   イメージファイルを選択した後は、これを実行します。
           |
           +-[NMI RESET]
           |   NMI リセットします。とりあえず使わないと思います。
           |
           +-[QUIT]
               エミュレータを終了します。
               UI で行った設定変更を保存します。

  [Joy/Mouse]-+-[Joystick]
              |    仮想パッドを有効にします。
              |
              +-[Mouse]
                   仮想マウスを有効にします。

  [FDD0]-+-[イメージファイル名] または [-- no disk --]
         |   イメージファイルを選択している場合はイメージファイル名が
         |   表示され、選択していない場合は no disk 表示となります。
	 |
         |   これを選択するとファイラモードに移行します。
         |   ファイラモードでは、設定キーでディレクトリの移動、
         |   イメージファイルの選択を行います。
         |   解除キーでファイラモードを終了します。
	 |
         +-[EJECT]
             イメージファイルを解除します。

  [FDD1]
           [FDD0] と同じです。
           ディスクドライブ2についての設定となります。

  [HDD0]
           [FDD0] と同じです。
           SASI HDD についての設定となります。

  [HDD1]
           [FDD0] と同じです。
           SASI HDD についての設定となります。
           HDD を 2 台以上接続する場合は、switch.x で HD_MAX の値を確認し、
           必要ならば変更してください。3 台以上の HDD を接続したい場合は
           config ファイルを直接編集してください。

  [Frame Skip]
           フレームスキップの設定をします。
           とりあえず Auto、とてつもなく早いマシンなら Full、
           遅いマシンは 1/2 -> ... -> 1/60 と変更してみてください。

  [Sound Rate]
           サウンドの出力周波数を設定します。
           値が大きいほど高音質になりますが、負荷がたかくなります。
           [No Sound] を選ぶと、音が鳴らなくなります。
           ※設定は次回起動時に有効になります

  [VKey Size]
           本設定は Android 版のみ有効です。 
           仮想パッド、仮想ボタンの大きさを指定します。

  [VBtn Swap]-+
              |  本設定は Android版 のみ有効です。 
              |  二つの仮想ボタンを入れ替えます。
              |  常にトリガー1が UI の設定キー、
              |  トリガー2が UI の解除キーとなるのでご注意ください。
	      |
              +-[TRG1 TRG2]
              |    画面上、左側がトリガー1、右側がトリガー2になります。
	      |
              +-[TRG2 TRG1]
                   画面上、左側がトリガー2、右側がトリガー1になります。

  [HwJoy Setting]-+-[Axis0(Left/Right):]
                  |   物理パッドの左右移動のパッドを設定します。
                  |   使用したい移動キーを左または右に動かしてください。
                  |   表示されている数字は現在の設定値です。
                  +-[Axis1(Up/Down):]
                  |   物理パッドの上下移動のパッドを設定します。
                  |   使用したい移動キーを上または下に動かしてください。
                  |   表示されている数字は現在の設定値です。
                  +-[Button0:]
                  |   物理パッドの TRG1 のボタンを設定します。
                  |   使用したいボタンを押してください
                  |   表示されている数字は現在の設定値です。
                  +-[Button1:]
                  |   物理パッドの TRG2 のボタンを設定します。
                  |   使用したいボタンを押してください
                  |   表示されている数字は現在の設定値です。
                  :
                  +-[Button7:]
                      物理パッドの TRG8 のボタンを設定します。
                      使用したいボタンを押してください
                      表示されている数字は現在の設定値です。

       ※項目を表示させた後、さらに選択ボタンを押すと設定モードにはいります。

  [No Wait Mode]
           [On] にすると同期をとらずに全速力で動きます。
           X68000 実機よりも早く動作してしまう場合があります。
           遅い装置で使うと効果があるかもしれません。
           通常は [Off] でよいです。

  [JoyKey]
           [On] にすると、物理キーボードのカーソルキーが Joystick の移動、
           z/x キーが Joystick のボタン代わりになります。


7. ToDo

  とりあえず、自分の予定と要望があったのを挙げてみました。
  他にもありましたら、blog のコメントでも Twitter でも構いませんので
  お知らせください。
  実現するかどうかは別ですが...

  以下は順不同です。

  ・実マウス対応
  ・仮想パッドのカスタマイズ (パッドの間を詰める?)
  ・物理キーボードのマッピングを手抜きしない (打てないキーをなくす)
  ・仮想キーボードの直接キータッチ対応
  ・ログメッセージ出力 (導入時、起動しない場合にあると便利)
  ・ファンタジーゾーン、サラマンダのショット音がおかしいのを修正 (難しい?)
  ・仮想ボタンデザイン
  ・PSPの終了処理速度を改善する
  ・menu/softkey のキーリピート
  ・utf-8なファイル名の表示 (iconv 使えない場合は面倒)
  ・性能改善

8. 履歴

   2013/08/02 PSP 版 Ver0.00 リリース
   2013/08/07 Android 版 Ver0.00 リリース
   2013/08/12 256x256, 512x512 の場合に画面比率4:3になるように修正 (Unix)
   2013/08/12 256x256 画面を横 2.666 倍、縦 2 倍の拡大表示に変更 (Unix)
   2013/08/12 画面描写を OpenGL ES 1.1 を使用する様に変更 (Android)
   2013/08/17 画面描写を HW Graphics Engine を使用する様に変更 (PSP)
   2013/08/19 ジョイスティックのサポートを追加 (PSP)
   2013/08/20 PSP 版 Ver.0.01 リリース
   2013/08/21 画面タッチによるジョイスティックのサポートを追加 (Android)
   2013/08/22 Android 版 Ver0.01 リリース
   2013/08/29 サウンド出力の追加 (Unix, Android)
   2013/08/29 Android 版 Ver0.02 リリース
   2013/08/30 プチノイズの原因となる、サウンドバッファの underrun が
              発生しないよう修正 (common)
   2013/08/30 CPU クロックを 222MHz から 333MHz に変更 (PSP)
   2013/08/30 サウンド出力の追加 (PSP)
   2013/08/31 PSP 版 Ver0.02 リリース
   2013/09/03 戻るボタン、メニューボタンでクラッシュするのを修正
                                                (Android, thanks to @173210)
   2013/09/03 常に横向き (landscape) 固定に変更 (Android, thanks to @173210)
   2013/09/04 スリープするとエミュレータが異常終了する問題を修正
                                                (Android, thanks to @173210)
   2013/09/04 バックキー 2 回タップでエミュレータを終了する様に変更 (Android)
   2013/09/04 機種により仮想キーが効かない問題を修正 (Android)
   2013/09/04 Android 版 ver0.03 リリース
   2013/09/17 menu UI を追加 (Unix系)
   2013/09/18 menu UI を追加 (PSP)
   2013/09/19 menu UI を追加 (Android)
   2013/09/20 PSP 版 Ver0.03 リリース
   2013/09/20 Android 版 Ver0.04 リリース
   2013/10/01 ソフトウェアキーボードの追加 (Android)
   2013/10/01 仮想キーの押下判定に少し遊びを持たせた (Android)
   2013/10/02 Android 版 Ver0.05 リリース
   2013/10/09 ソフトウェアキーボードの追加 (PSP)
   2013/10/10 PSP 版 Ver0.04リリース
   2013/10/15 プライオリティが text > BG の時に BG1 を未描画だったのを修正
   2013/10/15 ソフトウェアキーボード処理で keyup イベントも見るように修正
   2013/10/16 PSP 版 Ver0.05 リリース
   2013/10/16 Android 版 Ver0.06 リリース
   2013/10/21 Makefileの整理
   2013/12/16 メインメモリを 12MB に増加 (PSP 版は 4MB に増加)
   2013/12/16 iOS 版 Ver0.01、Android 版 Ver0.07、PSP 版 Ver0.06 リリース
   2014/01/12 Mac OS X で動作する様にした
   2014/01/19 仮想マウスのサポートを追加 (Android, iOS)
   2014/01/19 Android版 Ver0.08リリース
   2014/01/23 iOS 版 Ver0.02リリース
   2014/02/04 PSP 版 Ver0.07リリース
   2014/02/14 BG/Sprite更新時にゴミが残ったり更新しない場合があるのを修正
                                                         (thanks to ぴかよ氏)
   2014/02/14 BG/Spriteの優先順位がおかしい場合があるのを修正
                                                         (thanks to ぴかよ氏)
   2014/02/15 時刻取得時、月がずれるのを修正 (thanks to ぴかよ氏)
   2014/02/18 Android 版/PSP 版/iOS 版のアプリアイコンを追加
                                                         (thanks to なおき氏)
   2014/02/18 iOS 版 Ver0.03、Android 版 Ver0.09、PSP 版 Ver0.08 リリース
   2014/02/24 特殊プライオリティで前面に持ってくるグラフィックの色が黒く
              なってしまうのを修正
   2014/02/26 16x16 BG/Sprite 描画時に 8x8 の処理を呼び出していたのを修正
   2014/02/27 グラフィック画面高速クリア時に白でクリアしてしまう場合が
              あるのを修正
   2014/02/28 1024x1024 画面で、画面がずれる場合があったのを修正
   2014/03/04 addx.l 演算で carry flag の算出に eXtend bit を考慮して
              いなかったのを修正 (c68k MPU コア)
              これでドラオエ1 のフリーズが直るはず
   2014/03/04 ハードキーボードの未実装だったキーを実装した
   2014/04/01 物理パッドサポートを追加 (Unix/Android)
   2014/04/03 NoWaitMode 設定が保存されていなかったのを修正
   2014/04/03 1/16, 1/32, 1/60 の フレームスキップを追加
   2014/04/03 フレームスキップの設定が再起動すると 1/5 に戻るのを修正 (PSP)
   2014/04/03 JoyKey (キーボードによるパッド操作)の On/Off を UI に追加
   2014/04/05 不当命令の例外処理で、スタックに積む PC の値を該当命令ではなく
              該当命令の直後の値にしていたのを修正 (c68k MPU コア)
              これで、ソーサリアンが MPU を 68040 と誤認識する問題が直るはず
   2014/04/09 HDD イメージ (hdf) 対応
   2014/04/09 物理パッドの 8 ボタン対応 (Unix/Android)
   2014/06/17 px68k ディレクトリ /sdcard 以外も対応、外部ストレージ対応
                                                                    (Android)
   2014/06/25 外部ストレージ対応で、ファイルを選択時に異常終了するのを修正
                                                                    (Android)
   2014/06/25 イメージが一つもない場合に空のイメージを選択できる問題を修正
   2014/06/25 subx.l 演算で carry flag の算出に eXtend bit を考慮して
              いなかったのを修正 (c68k MPU コア)
              これで gcc  のフリーズが直るはず
   2014/07/03 サウンド出力ロジックの見直し(音が変になることがあるのを修正)
   2014/07/03 UI の FDD/HDD 番号を 0 origin に変更
   2014/07/03 2 台目の HDD イメージに対応
   2014/07/03 サウンドの出力周波数を UI で変更できるようにした
   2014/07/09 PX68K のバージョンを UI に表示するようにした


9. 免責

  本ソフトウェアを使用したことによる、いかなる損害も作者は責任を負いません。
  あくまでも自己責任での使用をお願い致します。


ひっそりぃ(hissorii)/sakahi
http://hissorii.blog45.fc2.com (ひっそりぃドットコム)
http://emuhani.seesaa.net      (えみゅはに - Emulator Hacking 日記)
GitHub: https://github.com/hissorii/px68k
iOS 版リポジトリ http://hissorii-com.appspot.com
Twitter: @hissorii_com