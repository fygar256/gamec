昔懐かしのGAME言語のインタプリタをCで書いてみました。

資料が少ないもので、オリジナルとは少し変わっていると思いますが、一応Linux上での実装ということで、ご勘弁いただきたいと思います。

GAME言語は今は廃れてしまいましたが、1980年前後にマイコンで手軽に使える処理系として一世を風靡しました。僕もGAME言語を参考に独自のインタプリタ・コンパイラを作って遊んでいたものです。

このプログラムはまだ未完成な所があり、エラーチェックなどが十分ではないです。実用とは程遠いものですが、まあ、遊びだと思って下さい。一応制御構文などは全部動きます。

コンパイル、インストール、実行

カレントディレクトリにgamelinux.cを置き、コンソールで、cc gamelinux.c -o gamelinuxとしてコンパイルて下さい。

インストールは、sudo cp gamelinux /usr/bin/として、/usr/bin/にgamelinuxを配置するだけです。

実行は、gamelinux [file.gm]として動かして下さい。

gamelinux では、シバンをつけることができます。シバンを付けて実行権を付けると、Linuxのシェルからスクリプトをコマンドのように実行できます。

シバンのサンプル

!/usr/bin/gamelinux

使い方

GAME言語のファイル拡張子はgmであるらしいので、ここではそれに則ります。

サンプルプログラムは、インタプリタ起動時に引数で指定すると読み込まれます。

コマンドプロンプトからは*ld filename.gmとして読み込んで下さい。#=1でrunです。

*がついているコマンドはオプショナルコマンドで、ユーザーが追加できるようにしてあります。（と言ってもインタプリタのソースを書き換える必要がありますが)

エディタはついてないです。行末が0xa（LF)で終わるテキストを読み込めます。

文法

・Jun Mizutani氏のGAME言語の文法を修正してアップします


<行番号>   1 〜 32767

<10進定数> 0 〜 65535

<16進定数> $0000 〜 $FFFF

<文字定数> "文字"

<変数名>   A 〜 Z または冗長形(ABC等 先頭1文字が有効)

<2バイト配列> ::= 変数名 ( <式> )

                  変数の値 + 2 * 式の値 のアドレスの内容を値とする．

<1バイト配列> ::= 変数名 : <式> )

                  変数の値 + 式の値 のアドレスの内容を値とする．

<定数> ::= <10進定数> | <16進定数> | <文字定数>

<変数> ::= <変数名> | <1バイト配列> | <2バイト配列>

<式> ::= <項> | <項> <二項演算子> <項>

<項> ::= <定数> | <変数> | <配列> |（ <式> ）| <単項演算子> <項>　|  $  | ?

              $ は一文字入力、? は数値入力

<二項演算子> ::= + | - | * | / | = | <> | < | > | <= | >=

                比較演算 は 真:1, 偽:0の値を取る．

<単項演算子> ::= - | + | % | ' | #

                 + は絶対値, % は直前に実行した除算の余り,

                 ' は乱数, #は否定．

<行> ::= <行番号> スペース <文> [ 空白 <文> ] 改行

         | <行番号> スペース以外の文字 コメント 改行

<文>

    *AB              オプショナルコマンド　この場合はABというコマンド

    <変数>=<式>     変数への代入

    #=<式>            <項>の値の行番号の文にジャンプ(GOTO)

                      行番号がなければ行番号より大きい最初の行へジャンプ

    #=-1              プログラムの終了(END)

    !=<式>            <項>の値の行番号のサブルーチンへジャンプ(GOSUB)

    ]                 サブルーチンから戻る(RETURN)

    ;=<式>            式の値が真の場合は次の文に進み，

                      偽の場合は次の行を実行．

    @                 DO

    @=(式)            UNTIL

    変数=初期値,終値　FOR

    @=式              NEXT

    /                 改行出力

    "文字列"          文字列出力

    ?=<式>            <項>の結果を数値出力 左詰め

    ??=<式>           <項>の結果を数値出力 16進4桁

    ?$=<式>           <項>の結果の下位1バイトを数値出力 16進2桁

    ?(n)=<式>         <項>の値の数値出力 n桁で右詰め

    $=<式>            <項>の値の下位バイトを文字コードとする１文字を出力

    .=<式>            <項>の値の下位バイトの数だけ空白を出力

    '=<式>            <項>の値で乱数シードを設定

このプログラムのオプショナルコマンドは次のとおりです。

TN Trace on

TF Trace off

LD load game program

QU quit

SH invoke shell

FM  for mode fm 0の時、for文はGAME言語オリジナルのように振る舞い、fm 1の時、普通の言語のように振る舞います。デフォルトは0です。


可搬性

一文字キー入力のgetch()関数だけがLinux固有のものです。getch()問題さえクリアできれば、ターミナルだったらどんな環境でも動くと思います。

履歴

・2023年1月31日 16ビット配列の取得の所にバグがあったので訂正。

・2023年2月1日　マイナーチェンジ＆名前をgamelinuxにする。

・2023年2月2日　バグ修正 ver 0.9

・2023年2月3日　getch()関数を内包 ver 0.9.1

・2023年2月4日　for文修正 ver 0.9.2

・2023年2月8日　for文の普通の言語モードで動作するのを　*FM 1に、GAME言語モードで動作するのを*FM 0にする。 ver 0.9.3

お願い

バグを見つけた方がいたら、ご一報下さい。m( )m
