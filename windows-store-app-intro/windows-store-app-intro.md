% Windows ストアアプリのデザイン・開発について
% Jun SAITO
% June 29, 2013

# Windows ストアアプリのデザイン・開発について

齊藤 潤 ([@ST63Jun_](https://twitter.com/ST63Jun_))

# 自己紹介

* 齊藤 潤 ([@ST63Jun_](https://twitter.com/ST63Jun_))
* 株式会社ユニキャスト所属
* Windows ストアアプリとRuby on Railsでアプリ開発してます

# Summary

* Winddows ストアアプリのデザイン - デザインの原則, タイル・ページ・アプリバー, MVVM, XAML, 非同期プログラミング
* サンプルコード解説 - ブログリーダーアプリのソースコードを読む
* 開発のポイント - 注意点、オープンソースプロジェクト

# はじめに

* Windows 8 から Windows ストア が搭載されました
    * iOS/Android と同様の、OSビルトインのアプリ流通プラットフォーム
    * コンシューマー向けOSのトップシェアを誇る Windows のユーザーを相手にアプリを販売することが可能
* 今日はそんな Windows ストアアプリのデザインと開発についてざっくり解説します
* おことわり：使用するプログラミング言語はC#を前提とします。

# Windows ストアアプリのデザイン

* デザインの原則
* Modern UI
* Model-View-ViewModelパターン
* データバインディング
* XAML (Extensible Application Markup Language)
* 非同期プログラミング

# デザインの原則

* Windows ストアアプリは以下の原則を参考にデザインすることが求められています：
* Pride in craftmanship - 作品へのこだわりを示す
    * ユーザーの目に触れることが多いアプリの細部を作り込みます
    * グリッド (GridView) を基に、バランス、対称性、階層構造を意識したデザインをします
* Be fast and fluid - 軽快かつ柔軟
    * ユーザーの操作に対して素早く応答します
    * アニメーションを適用することで、ユーザーに楽しみを与えるとともに、タッチ操作の視覚的なフィードバックを提供します
    * 処理をブロックする可能性のある遅い処理を非同期に実行するために、非同期APIを積極的に利用します

# デザインの原則 (つづき)

* Authentically digital - 真のデジタル化を心がける
    * 「ハードウェアとソフトウェアの機能を明確に示します。デジタル メディアの利点を最大限に活用します。物理的な境界を取り除いて、現実の世界より効率的で容易なエクスペリエンスを実現します。」
    * 美しいタイポグラフィや大胆で力強い色を使います
* Do more with less - より少い要素でより大きな効果を上げる
    * 1つの機能に突出した設計を目指します
    * クロム(枠線や水平線)でコンテンツを整理することを避けます
* Win as one - 全体で勝つ
    * ひとつのアプリで完結するのではなく、他のアプリやデバイス、システムと連携してユーザーの要求を満たします
    * UXガイドラインに従うことで、統一感のある操作感を提供します

# Modern UI

* Windows 8 ではユーザーインタフェースが矩形(タイル)を基調としたModern UIへと一新されました (旧称: Metro UI)
    * アイコンの代わりにタイルを使います
    * ウィンドウの代わりにページを使います
    * メニューバーの代わりにアプリバーを使います
* タイルで情報を表示し、そのタイルをタップするとさらに詳しい情報にジャンプする、というのが基本のパターンになります

# Model-View-ViewModelパターン

* iOSでは MVC (Model-View-Controller) パターンが基本の設計パターンでしたが、Windows ストアアプリでは MVVM (Model-View-ViewModel) パターンを使います
* Model
    * ビジネスロジックやアプリで使用するデータを表すオブジェクト
* View
	* ユーザーインタフェース
* ViewModel
	* Model の内容を View に橋渡し、またはその逆をするオブジェクト
* ModelとViewがViewModelを介してデータをやりとりする
    * ロジック・データ (Model) とUI (View) の分離がしやすく、疎結合なコードが書きやすい
* でもViewModelの実装が大変じゃ…
    * .NETの「データバインディング」を使うことで簡単に実装できます!
	
# データバインディング

* UI (View) のプロパティが変更されると、その変更が事前に設定 (バインド) したオブジェクト (ViewModel) のプロパティに直ちに反映される仕組み
  * 逆に、バインドしたオブジェクトの値を変更すると、UIのプロパティも変更させることも可能
* Windows ストアアプリでは、ViewModelオブジェクトをViewにバインドし、ModelオジェクトからViewModelを読み書きすることで、データをUIに表示したり、UIからからの入力データを受けとったりします

# XAML (Extensible Application Markup Language)

* Windows ストアアプリのViewはXAMLというXMLベースの記述言語で構築します
* 前述のデータバインディングはXAMLコードの中で設定することができます
* Visual Studio でXAMLファイルを開くと、マウス操作でUIを作成できるデザイナーが表示されますが、慣れてくるとXAMLを直接書いたほうが早かったりします
* XAMLファイルにはコードビハインド呼ばれる、対となるC#ソースコードが存在します
    * テキストの全選択やページの移動といったUIコントロールの制御のためのコードはコードビハインドの中に記述します

# 非同期プログラミング

* 軽快かつ柔軟なUIの体験を提供するためには、UIスレッドを長時間ブロックするようなプログラムは避ける必要があります
    * しかし、ネットワーク通信や多量のディスクアクセスといった処理は時間がかかります
* このような問題を解決するには、このような重い処理をUIスレッドと別のスレッドで非同期で実行する必要があります
    * でも、マルチスレッドプログラミングはめんどくさい…
* C#ではこのような非同期での処理の実行を簡単に行うため async/await という糖衣構文が容易されています


# サンプルコード解説

* MSDNのチュートリアルで紹介されているブログリーダーのソースコードを解説します
    * チュートリアル: http://msdn.microsoft.com/ja-jp/library/windows/apps/br211380.aspx
    * ソースコード: http://code.msdn.microsoft.com/windowsapps/RSS-Reader-affe3358
* 解説のポイント
    * App.xaml(.cs)
	* MainPage.xml(.cs)
	    * データバインディング
        * 値コンバーター
		* スタイル
	* FeedData.cs
        * ViewModelオブジェクト

# 開発のポイント

* 複数のビューステート (表示状態) をサポートする必要がある
    * Windows 8 では 縦置き (ポートレート)・横置き (ランドスケープ) のほかにも、複数ページを並べたときに遷移するフィル・スナップという表示状態があります
	* アプリの審査ではこれらの4つのビューステートにしてもアプリが正しく機能するかということが検査されます
	* 一画面にコントロールを沢山配置するとこの対応が大変になるため、ページごとの機能の切り分けが重要になります
* 標準のデータベースAPIがない
    * 今のところ iOS の CoreData API のような標準のデータベースAPIがありません
	* 現在は sqlite-net というユーザー製のオープンソースの SQLite ライブラリがよく使われており、開発も活発です
	    * https://github.com/praeclarum/sqlite-net
* バリデーションは自分で実装する必要がある
    * WPF/Silverlight ではXAMLコードにバリデーションを行うためのクラスを指定することができましたが、Windows 8 では削除されてしまいました
    * 今のところ Windows 8 向けのバリデーションのライブラリはない？ので自分で実装す必要がある
* 時にはガイドラインを破ることも必要
    * Windows 8 との統一感を出すためには、Microsoft が提示する Modern UI のUXガイドラインに従う必要があります
	    * http://msdn.microsoft.com/ja-JP/library/windows/apps/hh465424
	* 一方で、なんでもフラットに統一すると、統一感はでるけど個性がなくなる
	* 統一感を重視しつつもく、時には大胆にガイドラインを破るバランス感覚が重要です

# まとめ

* Windows ストアアプリのデザインについて説明しました
    * 原則
    * Modern UI
    * UXガイドライン
    * Model-View-ViewModelパターン
    * データバインディング
    * XAML (Extensible Application Markup Language)
    * 非同期プログラミング
* Windows ストアアプリのサンプルコードを解説しました
	* Visual Studio による開発
    * ViewModelの実装
	* データバインディングの方法
	* スタイルによる見た目の設定
* 開発のポイントについて説明しました
    * 複数のビューステート (表示状態) をサポートする必要がある
    * 標準のデータベースAPIはなく、sqlite-netが選択肢
    * バリデーションは自分で実装する必要がある
    * 時にはガイドラインを破るバランス感覚も重要