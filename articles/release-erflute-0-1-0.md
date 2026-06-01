---
title: "デスクトップ版erflute（0.1.0）をリリースしました！"
emoji: "😎"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["erflute", "OSS"]
published: true
---

本記事は[こちらのブログ](https://dissection.skai273.dev/posts/2026/06/01/release-erflute-0.1.0/release-erflute-010/)の転用となります。

## はじめに

少し時間が経ってしまいましたが、今年の2月にerfluteのデスクトップ版をリリースしましたので、その告知をします。  
機能的には**読み取り専用**のER図作成ツールです。  
去年の10月頃から着手し、空き時間に少しずつ実装することでようやく最低限の機能が形になりました。  

https://github.com/erflute/erflute

## erfluteとは？

そもそものところにはなりますが、erflute自体は自分が1から作成したツールではありません。  
Java/Web界隈ではご存知の人も多いかもしれませんが、もともとeclipse pluginとして動くER図作成ツールとして人気のあったOSSです。  
jfluteさんがERMasterという元ツールをフォークして作成されたもので、dbfluteというORマッパーの関連ツールという位置づけになっています。  
[eclipse版erfluteの公式ドキュメント](https://dbflute.seasar.org/ja/manual/function/helper/erflute/index.html)
![eclipse版erflute](https://dbflute.seasar.org/data/snapshot/helper/erflute/erflute-introduction-overview-snapshot.png)

見た目はシンプルですが、DDL出力、カーディナリティ表現、Outline検索、仮想ダイアグラムなど、DB設計の業務で必要な機能は十分に揃っているのが特徴です。  
大規模なアプリケーションでも使用されていた実績があり、自分が新卒で入社した会社の現場でも使用していました。  

## eclipse版erfluteの問題点

しかしeclipse版のerfluteには、いくつかの問題点があります。  

1. eclipse上でしか動かすことが出来ない  
eclipseを使ってもとから開発しているユーザーや現場なら問題にならないかもしれませんが、それ以外のツールを使っているケースも多いはずです。  
そうなると、erfluteを使うためだけにeclipseをPCにインストールしなければならないということが起こりえます。  
実際に自分の新卒の現場でもメインの開発はIntelliJ、DB設計のときのみeclipseからerfluteを開くということをやっていました。  
Java以外の言語を使い開発をしている現場では、なおさらこの問題が多くなることが予想されます。  
2. 特定バージョンのeclipseでうまく動かないことがある  
[こちらの記事](https://dbflute.seasar.org/ja/manual/function/helper/erflute/index.html#eclipse202012)で紹介されていますが、新しいバージョンのeclipseではうまく動かいないケースも確認されています。  
上記記事とはまた別ですが、自分はLinux環境でeclipse版erfluteを立ち上げたところ、かなり挙動が不安定になることも確認しました。  


## デスクトップ版erfluteの作成

このような問題は、メンテナの継続が難しいOSSプロジェクトにおいては残念ながら起きがちなのが実情です。  
そこで、自分自身の腕試しと、過去にとてもお世話になったOSSということもあり、デスクトップ版erfluteを1から作ってみることにしました。  
これだけ高機能なツールの全機能を最初から実装に取り掛かると果てしないので、今回は**読み取り専用**ということで最小限の機能実装をしています。  
ここからは主な機能、特徴を紹介していきます。  

### クロスプラットフォームのスタンドアロンアプリケーション

技術スタックとして、Tauriを使うことでデスクトップアプリとして構築し、バックエンドRust、フロントエンドTypeScript + Reactで実装をしています。  
Windows、MacOS、Linuxのそれぞれのインストーラーを配布しているため、インストール後はスタートメニューからerfluteのスタンドアロンアプリケーションを立ち上げ、動かすことが出来ます。  
[デスクトップ版インストーラー](https://github.com/erflute/erflute/releases)


### 既存ermファイルの読み込み、ダイアグラムの表示

eclipse版erfluteでは作成したER図をerm拡張子の独自ファイルに保存する仕様となっています。  
デスクトップ版はこれと互換性を持たせる形で開発しているため、既存ermファイルからER図を表示させることが出来ます。  
具体的には、ナビゲーションメニューよりファイルを選択することでテーブルスキーマのダイアグラムが表示されます。  
表示デザインはeclipse版のerfluteとかなり近い形にしました。  
![open erm](https://dissection.skai273.dev/assets/2026/06/01/release-erflute-0.1.0/open-erm.gif)

機能としての詳細は下記となります。  
- テーブルの表示  
基本的にeclipse版と同じ情報が網羅されています。  
![table and relation](https://dissection.skai273.dev/assets/2026/06/01/release-erflute-0.1.0/table-and-relation.png)
- リレーションとカージナリティの表示  
こちらもelipse版と同じく、外部キー制約が存在するテーブルには参照を示すために線で関係を図示しています。  
カージナリティも外部キーのカラムとのレコード数の対応関係を0:1、0:N、1:1、1:Nの4パターンで示しています。  
- セルフリレーションの表示  
カージナリティの一種ですが、参照する外部テーブルが自分自身の時に下記のように表示されます。  
![self relation](https://dissection.skai273.dev/assets/2026/06/01/release-erflute-0.1.0/self-relation.png)
- ジェスチャー操作のサポート  
これはeclipse版にはない新規機能ですが、マウスやトラックパッドによるジェスチャー操作で画面の拡大縮小や移動が行えるようになっています。  
これにより直感的にER図を閲覧することが出来ます。  
  - 画面の拡大縮小
    ![zoom in out](https://dissection.skai273.dev/assets/2026/06/01/release-erflute-0.1.0/zoom-in-out.gif)
  - 画面の移動
    ![drag](https://dissection.skai273.dev/assets/2026/06/01/release-erflute-0.1.0/drag.gif)

### Table Information、Relationship Informationのダイアログ表示

テーブルとリレーションの詳細情報を表示するダイアログもeclipse版と同様に表示可能です。  

- Table Information  
該当テーブルのヘッダー部分をダブルクリックすると表示されます。  
eclipse版と同様に、テーブルの各カラム、共通カラム、複合ユニークキー、インデックスの情報などを確認することが可能です。  
![show table info dialog](https://dissection.skai273.dev/assets/2026/06/01/release-erflute-0.1.0/show-table-info-dialog.gif)

- Relationship Information  
該当リレーションをダブルクリックすると表示されます。  
こちらもeclipse版と同じように外部キー制約で参照しているカラムやカージナリティの詳細などが確認可能です。  
![show relation info dialog](https://dissection.skai273.dev/assets/2026/06/01/release-erflute-0.1.0/show-relation-info-dialog.gif)

これらを持ってテーブルとリレーションに関する最低限の情報はほとんど閲覧ができるようになっています。  

## おわりに

今回はerfluteのデスクトップ版ということで作ってみました。  
読み取り機能しかないですが、編集するつもりはなくER図の確認だけを目的にするなら現場でも使うことができるのでは？と思っています。  
eclipse版のerfluteを使ったことがある方は、ぜひ使っていただけると嬉しいです（フィードバックも大歓迎です）！  
今後も優先度の高い機能から追加し、開発を続けていくつもりなので、今後もよろしくおねがいします。

