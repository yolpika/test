# OO{D,A}の五大原則のお話

# アジェンダ

- 五大原則とは
- OCP
- SRP
- DIP
- ISP
- LSP

---

# 五大原則とは

以下の五つ:

- **S**RP：単一責務の原則
- **O**CP：開放-閉鎖の原則
- **L**SP：リスコフの置換則
- **I**SP：インターフェース分離の原則
- **D**IP：依存関係逆転の原則

頭文字を並べると「SOLID」になって覚えやすいです。

でも、より重要な順番に説明します。

---

# OCP - 開放-閉鎖の原則

Open-Close Principle ： 開放-閉鎖の原則

## 定義

> SOFTWARE ENTITIES (CLASSES, MODULES, FUNCTIONS, ETC.)
SHOULD BE OPEN FOR EXTENSION, BUT CLOSED FOR
MODIFICATION.

> クラス、モジュール、関数といったソフトウェアの構成要素は、拡張に対して「開いて」おり、かつ変更に対して「閉じて」いなければならない。

---


# 開いて、閉じて・・・


---

## こういうこと

図形を描くプログラムを作っているとします。円形や矩形を描かねばなりません。
まずは、まずいやり方から。

    !c++
	class Shape
	{
		int shapeType;
	};
    class Circle : public Shape
	{
		Coord center;  // 中心座標
		double radius; // 半径
	};
	class Rectangle : public Shape
	{
		Coord topleft; // 左上座標
		Size size;     // 幅と高さ
	};

---
## (承前)

    !c++
    // 以下２関数はどこか別のところでいい感じに実装されているとする。
    void DrawCircle(Circle c);
    void DrawRectangle(Rectangle r);
    
    void DrawAllShapes()
    {
        ...
        
        for (i = 0; i < NUMBER_OF_SHAPETYPE; i++)
        {
            // 何らかの形で図形をShapeList[]の形で扱えるようにしたとする
            switch (ShapeList[i].shapeType)
			{
			case SHAPETYPE_CIRCLE:	
				DrawCircle((Circle)ShapeList[i]);
				break;
			case SHAPETYPE_RECTANGLE:
				DrawRectangle((Rectangle)ShapeList[i]);
				break;
			}
		}
	}

---

# 何がまずい？

---

# いろいろありますが、

---

# 一番ダメージが大きいのはこれ・・・

---

# 図形が増えるごとに変更が必要

---

    !c++
	void DrawAllShapes()
	{
		...
		// 何らかの形で図形をShapeList[]の形で扱えるようにしたとする
		for (i = 0; i < NUMBER_OF_SHAPETYPE; i++)
		{
			switch (ShapeList[i].shapeType)
			{
			case SHAPETYPE_CIRCLE:	
				DrawCircle((Circle)ShapeList[i]);
				break;
			case SHAPETYPE_RECTANGLE:
				DrawRectangle((Rectangle)ShapeList[i]);
				break;
			}
		}
	}


図形を描くプログラムというお題なので、サポートする図形が増えるというのはかなり高確率で発生しそうな拡張と言えます。でも、関数`DrawAllShapes`は、三角形(`Triangle`)をサポートしたくなったら変更が必要になります。

`DrawAllShapes`は拡張による影響を受けやすく、これは__拡張に対して開いていない__  わけです。


---

# で、どうするか？

---

# こんな風にします。

---

まず、基底クラスたる`Shape`を抽象化します。

    !c++
    class Shape
    {
    public:
        virtual void DrawShape() = 0;
	};

その上で`Circle`と`Rectangle`に`Shape`インターフェースを実装します。

    !c++
	class Circle : public Shape
	{
    public:
		void DrawShape(); // Circleなりの描画 
	};

    class Rectangle : public Shape
	{
    public:
		void DrawShape(); // Rectangleなりの描画 
	};

.notes: コンストラクタ／デストラクタは省略しています

---

#とやっておいて

---
`DrawAllShapes`をこのように直します。

    !c++
	void DrawAllShapes()
	{
		...
		// 何らかの形で図形をShapeList[]の形で扱えるようにしたとする
		for (i = 0; i < NUMBER_OF_SHAPETYPE; i++)
		{
			ShapeList[i]->DrawShape();
		}
	}

---

# 何がよくなった？

---

# 何がよくなった？

    !c++
	void DrawAllShapes()
	{
		...
		// 何らかの形で図形をShapeList[]の形で扱えるようにしたとする
		for (i = 0; i < NUMBER_OF_SHAPETYPE; i++)
		{
			ShapeList[i]->DrawShape();
		}
	}

- 図形が増えるにしたがって長たらしくなりそうだったswitch..case文が姿を消しました。
- もし今後新たな図形Triangleをサポートすることになっても、DrawAllShapesは無改造で済みます。

`DrawAllShapes`は拡張に際して変更が不要、つまり__ 拡張に対して開いている __  状態になりました。

---

# 留意点

- もちろんいかなる場所にも、いかなる状況でも100%適用可能、というものではありません。
- 肝心なのは見極めです：
    + 安定していて変更の必要性が低いのはどこか
    + 拡張を見込んでおくべき部分はどこか
- そのクラスの責務/そのクラスに対する要求に因る
  + 安定している＝抽象化対象、拡張の可能性があるところ＝派生

---


# SRP - 単一責務の原則

Single Responsibility Principle ： 単一責務の原則

## 定義

> THERE SHOULD NEVER BE MORE THAN ONE REASON FOR A
> CLASS TO CHANGE.

> あるクラスを変更しなければならないとしたら、
> その理由はただ一つであるべきである。

---

#  二つ以上の責務を負っているとしたら

あるクラスの責務は、そのクラスを含むソフトウェアに対する要求事項と結びついています。
ということは、要求事項の変化に対して変更を余儀なくされる確率が高くなるということです。

柔軟に変更を受け入れることは重要ですが、その都度あちこちに変更の影響が及ぶのは避けねばなりません。
そのような設計は柔軟というよりむしろ脆い設計と言えます。

---

# 柔軟さと頑健さ

OCPで、拡張に「開いておくべき」と主張しておきながら、変更の確率を下げたいというのは何だか矛盾して聞こえるかもしれません。

しかし、基本的には一度作ってテストしたコードは変えたくないのです。オブジェクト指向だからと言ってこの考え方は変わりません。

C言語のような、非オブジェクト指向言語を用いる場合と何が違うのでしょうか。

それは、クラス分割と継承によって変更や拡張によって被る影響範囲を局所化しやすい点ではないでしょうか。

そうであれば、その特徴を活かすような使い方をしなければなりません。

柔軟さと脆さ、頑健と硬直の違い、といえるかもしれません。

---

# ところで責務って？

---

# クラスの責務

責務＝役割という意味が一般的ですが、どうもその軸で考えてゆくといつの間にか複数の責務が一つのクラスに吹き溜まりです傾向があると思います。

責務＝変更の理由、と捉えるとよいでしょう。

---

# よく見るモデムの例

最近はモデムという装置そのものを目にすることが少なくなったため、イメージしにくい人も多いでしょう。
モデムは（乱暴にいうと）加入者電話回線を使ってコンピュータ同士の通信を行うための終端装置です。

そのため、回線接続のためのダイヤル(Dial-Up)し、データを送受信(Send/Receive)し、そして切断(Hang-Up)します。

これをそのままクラス化すると以下のようになるでしょう。

    !c++
    class Modem {
    public:
        DialUp();
        Send();
        Recv();
        HangUp();
    }

---

# 何がいけない？

一見、モデムという装置がもつ機能をうまい具合にまとめたように見えますし、そのままでもよい場合もあります。

    !c++
    class Modem {
    public:
        DialUp();
        Send();
        Recv();
        HangUp();
    };

先ほどの「責務」の定義に照らしてみると、その変更理由から以下の２つに大別できます。

- 回線接続方法の変更
    - DialUp()とHangUp()を変更
- データ通信方法の変更
    - Send()とRecv()を変更

つまり、Modemクラスは回線接続とデータ通信という二つの責務を負っていると言えます。

---

# どうすべき？

二つの責務をそれぞれのクラスに分離します。

    !c++
    class LineConnection {
    public:
        DialUp();
        HangUp();
    };
    class DataChannel {
    public:
        Send();
        Recv();
    };

`LineConnection`クラスは回線接続機能に注力し、`DataChannel`クラスはデータ通信機能に注力しているので、それぞれの変更理由は一つずつになります。

---

# C++限定

前ページの解決例は、モデムを使った通信プログラムを書く側にとってはやや使い辛いと言えます。
    （設計原則を踏襲していて「綺麗」であったとしても使い勝手も重要ですので）

C++は多重継承を許しているため、両者をインターフェース化した上で継承することでまとめ直すことができます。

    !c++
    class LineConnection {
    public:
        virtual DialUp() = 0;
        virtual HangUp() = 0;
    };
    class DataChannel {
    public:
        virtual Send() = 0;
        virtual Recv() = 0;
    };
    class ModemImpl : public LineConnection, public Datachannel {
        ...
    };

Javaでは多重継承を使うことはできませんが、extendsとimplementsを適切に使えば同様のことは実現できるものと思います。


---

# DIP - 依存関係逆転の原則

Dependency Inversion Principle ： 依存関係逆転の原則

## 定義
> A. HIGH LEVEL MODULES SHOULD NOT DEPEND UPON LOW
LEVEL MODULES. BOTH SHOULD DEPEND UPON ABSTRACTIONS.
> B. ABSTRACTIONS SHOULD NOT DEPEND UPON DETAILS. DETAILS
SHOULD DEPEND UPON ABSTRACTIONS.
>
> A. 上位のモジュールは、下位のモジュールに依存すべきでない。
> B. 抽象層は具象層に依存すべきではない。具象層は抽象層に依存すべきである。

---


# ISP - インターフェース分離の原則

Interface Segregation Principle ： インターフェース分離の原則

## 定義

> CLIENTS SHOULD NOT BE FORCED TO DEPEND UPON INTERFACES
THAT THEY DO NOT USE.

---


# LSP - リスコフの置換則

Liskov's Substitution Principle ： リスコフの置換則

## 定義

> FUNCTIONS THAT USE POINTERS OR REFERENCES TO BASE
CLASSES MUST BE ABLE TO USE OBJECTS OF DERIVED CLASSES
WITHOUT KNOWING IT.

---

# けずるかも；ソフトウェアの設計について

設計書を書くことそのものや、クラス図やシーケンス図を書くことが設計ではありません。
開発用のエディタや統合開発環境を使ってコードを書く行為はソースコードの製造ではありません。

ソフトウェアの開発における「製造」はコンパイラとリンカがソースコードを(あるいはインタープリタが中間コードを)
実行環境にあわせたマシンコードに変換する過程であり、よほど大きなプログラム(500KLineとか)でない限り、
昨今の開発環境であれば、一瞬で終わってしまいます。

ではコードを書いている時間はいったい何をしている時間なのでしょうか。
ソースコードを書いている時間の大部分は何らかの設計判断をし、それを「製造」の元ネタである
ソースコードに書き起こしているわけです。「製造」の元ネタが「設計書」であるなら、
ソースコードは設計書です。

この過程、特に設計判断を下す過程は、判断の根拠となる情報収集、制約条件の確認、
プログラミング言語による判断結果の表出、必要なトライアンドエラーといった設計に
伴う活動が__ほぼ頭の中だけで__行われています。設計の良し悪しは開発者の頭の中に
構築されたモデル次第ということになります。

他方、慎重な開発者であれば、自分一人の考えだけでは判断の方向性が偏ってしまったり、
そのせいで考慮すべき視点が足らなくて失敗する恐れがあることを知っています。
そのため彼らは他の人の意見を求めることで設計を磨きます。これがレビューです。

他人から意見を求めるためには、自分がどのように問題を捉えているか、いかなる論拠に
基づいてどのように判断したかを理解してもらう必要があります。その上で足りない点や
もし理解が誤っていれば訂正を受けることや、よりよいアイディアについて議論することも
できます。

他人に自分の考えを理解してもらうためには、言葉だけでなくその言葉を補足し、
理解の助けとなる図、静止画・動画などあらゆる媒体を利用する必要があります。
目的は他人に考えを理解して意見をもらうことにあるのですから、華麗な文体と書体
である必要も、ラファエル前派の絵画のようである必要もありません。

ホワイトボードに描いたラフなスケッチと、しゃべりをガイドする簡単なレジュメと、
何としても理解してもらうという情熱があれば、図入りの仕様書２００頁よりずっと
確実にイメージを伝えることができます。


--- 
