## Key Words

MVC（Model -View-Controller）、Module、Inheritance（継承）

## Notes

**MVC**

Model-View-Controller 

**Model**

プログラムがやることを指す。

UIとは関係ない

例）計算機では計算する機能を指す。

**View**

UserInterface(UI)を指す。

Userとやりとりをする。

例）計算機だとボタンや画面を指す。

**Controller**

ModelとViewを連携させる。

UIで何が起きてるのか聞いて判断しModelをアップデートする。

Modelに変更があった場合Viewをアップデートする。

アプリ全体のInterfaceを担当する。

**Module**

Classたちを含めてグループに集めておいた物

import UIKit

-> UIKit Moduleを呼び込む

UIKitではButton, TextFiled みたいな全てのUIが含まれている。

他に例をいうとCore Services(Networking,Databasingなど)が必要なとき

Foundationをimportする。

**Inheritance(継承)**

全てのMVCのControllerは直接的や間接的にUIConterollerから継承しなければならない

コード作成例）

```swift
class ViewController: UIViewController
```

ViewControllerというclassはUIViewControllerから継承されている

## Summary

今日はMVCモデルやModule、Inheritanceについて知ることができた。

**MVC**はアプリをModel,View,Controllerという構造で構成する設計手法で

Modelは実際のロジックを担当

ViewはUIやユーザとやりとり

ControllerはViweやModelに変更が起きた場合その変更事項をアップデートするように

それぞれの役割を分担させて構造をもっと把握しやすいと思った。

**Module**はclassたちの集まりで他のファイルでもimportを作成すれば活用することができるようになる

基本的にUIを作成するときはUIKit

機能を具現するためにCoerServicesが必要なときはFoundation

をimportして開発が行う

最後に**Inheritance**は作るclassの名前の後ろに：[継承するclassの名前]を書く

コード作成例）

```swift
class ViewController: UIViewController
```

ViewControllerというclassはUIViewControllerから継承されている