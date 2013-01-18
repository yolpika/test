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

図形を描くプログラムを作っているとします。円形や矩形を描かねばなりません。図形を描くにあたって図形を描く順序が決まっているものとします。
まず、まずい考え方。

    !c++

	class Shape
	{
		const 
		int shapeType;
	}

	class Circle
	{
		Coord center;  // 中心座標
		double radius; // 半径
		   ...
	}

	class Rectangle
	{
		Coord topleft; // 左上座標
		Size size;     // 幅と高さ
		   ...
	}

---
## (承前)

	!c++

	// 実装はどこか別のところにあるとする。
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

図形を描くプログラムというお題でしたから、サポートする図形が増えるというのはかなり高確率で発生しそうな拡張と言えます。でも、関数DrawAllShapesは、Circle及びRectangle以外に、Triangleをサポートしたくなったら変更が必要になります。

この状況をして、DrawAllShapesは拡張に対して閉じている、というのです。


---


---

# SRP - 単一責務の原則

Single Responsibility Principle ： 単一責務の原則

## 定義

> THERE SHOULD NEVER BE MORE THAN ONE REASON FOR A
> CLASS TO CHANGE.

---

# DIP - 依存関係逆転の原則

Dependency Inversion Principle ： 依存関係逆転の原則

## 定義
>A. HIGH LEVEL MODULES SHOULD NOT DEPEND UPON LOW
LEVEL MODULES. BOTH SHOULD DEPEND UPON ABSTRACTIONS.

> B. ABSTRACTIONS SHOULD NOT DEPEND UPON DETAILS. DETAILS
SHOULD DEPEND UPON ABSTRACTIONS.

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
