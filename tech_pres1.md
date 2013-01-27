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


# 開いて、閉じて・・・は？


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

- 
---

---

# SRP - 単一責務の原則

Single Responsibility Principle ： 単一責務の原則

## 定義

> THERE SHOULD NEVER BE MORE THAN ONE REASON FOR A
> CLASS TO CHANGE.

> あるクラスを変更しなければならないとしたら、
> その理由はただ一つであるべきである。

---

# TEST

--

# DIP - 依存関係逆転の原則
T
Dependency Inversion Principle ： 依存関係逆転の原則

## 定義
> A. HIGH LEVEL MODULES SHOULD NOT DEPEND UPON LOW
LEVEL MODULES. BOTH SHOULD DEPEND UPON ABSTRACTIONS.
> B. ABSTRACTIONS SHOULD NOT DEPEND UPON DETAILS. DETAILS
SHOULD DEPEND UPON ABSTRACTIONS.
>
> A. 上位のモジュールは、下位のモジュールに依存すべきでない。
> B. 

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

