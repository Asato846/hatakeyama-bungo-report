# 詳細設計書の作成について

予定期間：2025年9月5日 ~ 9月10日 予定　<br>
実施期間：2025年9月10日 ~ 9月11日 完了<br><br>

## DB設計

* ログインするユーザー
* 商品
* 購入状態
を管理するテーブルを作成しました。

<img src="../images/section2-3/section2-3.png" width="500">

<div class="page"/>

## ログイン

```mermaid
sequenceDiagram
autonumber

participant ItemsIndex as 商品一覧Vue
participant LoginIndex as ログインvue
participant webphp as web.php
participant ItemsController as ログインController
participant ItemsModel as ルートServise
participant MySQL as MySQL

ItemsIndex ->>+ LoginIndex: リンクで遷移 login()
LoginIndex ->>+ webphp: login()
webphp ->>+ ItemsController: ルーティング
ItemsController ->>+ ItemsModel: store() session
ItemsModel ->>+ MySQL : select()
MySQL -->- ItemsModel: 抽出結果
ItemsModel -->>- ItemsController: ID・PWD照合
ItemsController -->>- webphp: セッション情報  or 失敗 return()
webphp -->>- ItemsIndex: セッション情報
webphp -->> LoginIndex : 失敗
```

<div class="page"/>

## パスワードリセット

```mermaid
sequenceDiagram
autonumber

participant ItemsIndex as 商品一覧Vue
participant LoginIndex as ログインvue
participant ForgotPassword as パスワードリセットVue
participant webphp as web.php
participant ItemsController as パスワードController
participant ItemsModel as ユーザーModel
participant MySQL as MySQL


LoginIndex ->>+ ForgotPassword: リンクで遷移 [password.request]
ForgotPassword ->>+ webphp : update()
webphp ->>+ ItemsController: ルーティング
ItemsController ->>+ ItemsModel: create() 新しいパスワード
ItemsModel -->+ MySQL : インスタンス作成
MySQL -->- ItemsModel : インスタンス
ItemsModel -->>- ItemsController: インスタンス
ItemsController -->>- webphp: セッション情報 return()
webphp -->>　ItemsIndex : セッション情報
```

<div class="page"/>

## ユーザー作成

```mermaid
sequenceDiagram
autonumber

participant ItemsIndex as 商品一覧Vue
participant LoginIndex as ログインvue
participant RegisterIndex as 登録vue
participant webphp as web.php
participant ItemsController as 登録Controller
participant ItemsModel as ユーザーModel
participant MySQL as MySQL


LoginIndex　->>+ RegisterIndex:　リンクで遷移 register()
RegisterIndex ->>+ webphp: register()
webphp ->>+ ItemsController: ルーティング
ItemsController ->>+ ItemsModel: create() 
ItemsModel ->>+ MySQL : インスタンス作成
MySQL -->- ItemsModel: インスタンス
ItemsModel -->>- ItemsController: インスタンス
ItemsController -->>- webphp: セッション情報  or 失敗 return()
webphp -->>- ItemsIndex: セッション情報
webphp -->> RegisterIndex : 失敗
```

<div class="page"/>

## 商品一覧・検索

```mermaid
sequenceDiagram
autonumber

participant ItemsIndex as 商品一覧Vue
participant webphp as web.php
participant ItemsController as 商品管理Controller
participant ItemsModel as 商品管理Model
participant MySQL as MySQL

ItemsIndex ->>+ webphp:商品検索 index("value")
webphp ->>+ ItemsController: ルーティング
ItemsController ->>+ ItemsModel: select("value")
ItemsModel ->>+ MySQL: 条件付SELECT発行
MySQL -->>- ItemsModel: 抽出データ
ItemsModel -->>- ItemsController: 抽出データ
ItemsController -->>- webphp: return()
webphp -->>- ItemsIndex: JSON形式の抽出データ
```

<div class="page"/>

## 商品登録

```mermaid
sequenceDiagram
autonumber

participant ItemsIndex as 商品一覧Vue
participant ItemsCreate as 商品登録Vue
participant webphp as web.php
participant ItemsController as 商品管理Controller
participant ItemsModel as 商品管理Model
participant MySQL as MySQL

ItemsIndex ->>+ ItemsCreate: リンクで遷移 create()
ItemsCreate ->>+ webphp: 商品登録 押下 store()
webphp ->>+ ItemsController: ルーティング
ItemsController ->>+ ItemsModel: create()
ItemsModel ->>+ MySQL: インスタンス作成
MySQL -->>- ItemsModel: インスタンス
ItemsModel -->>- ItemsController: インスタンス
ItemsController -->- webphp: return()
webphp -->>- ItemsIndex: ①初期表示処理へ 
```

<div class="page"/>

## 商品編集

```mermaid
sequenceDiagram
autonumber

participant ItemsIndex as 商品一覧Vue
participant ItemsShowall as 商品詳細Vue
participant ItemsEdit as 商品編集Vue
participant webphp as web.php
participant ItemsController as 商品管理Controller
participant ItemsModel as 商品管理Model
participant MySQL as MySQL


ItemsIndex ->>+ ItemsShowall: リンクで遷移 : show()
ItemsShowall ->>+ ItemsEdit: リンクで遷移 : edit()
ItemsEdit ->>+ webphp: 更新する 押下 update()
webphp ->>+  ItemsController: ルーティング
ItemsController ->>+ ItemsModel: save()
ItemsModel ->>+ MySQL: UPSERT発行※
MySQL -->>- ItemsModel: 成功可否
ItemsModel -->>- ItemsController: 成功可否
ItemsController -->>- webphp: return()
webphp -->>- ItemsIndex: ①初期表示処理へ 
```

<div class="page"/>

## 商品削除

```mermaid
sequenceDiagram
autonumber

participant ItemsIndex as 商品一覧Vue
participant ItemsShowall as 商品詳細Vue
participant webphp as web.php
participant ItemsController as 商品管理Controller
participant ItemsModel as 商品管理Model
participant MySQL as MySQL


ItemsShowall ->>+  webphp: 削除する 押下 destroy()
webphp ->>+ ItemsController: ルーティング
ItemsController ->>+ ItemsModel: delete()
ItemsModel ->>+ MySQL: DELETE発行
MySQL -->>- ItemsModel: 削除レコード数
ItemsModel -->>- ItemsController: 削除レコード数
ItemsController -->- webphp: return()
webphp -->>- ItemsIndex: ①初期表示処理へ 
```

<div class="page"/>

## 商品購入初期表示

```mermaid
sequenceDiagram
autonumber

participant PurchaseIndex as 商品購入(カート)Vue
participant webphp as web.php
participant ItemsController as 商品購入Controller
participant ItemsModel as 商品購入Model
participant MySQL as MySQL


PurchaseIndex ->>+ webphp:初期表示 index()
webphp ->>+ ItemsController: ルーティング
ItemsController ->>+ ItemsModel: select("セッションID","購入状態0")
ItemsModel ->>+ MySQL: 条件付SELECT発行
MySQL -->>- ItemsModel: 抽出データ
ItemsModel -->>- ItemsController: 抽出データ
ItemsController -->>- webphp: return()
webphp -->>- PurchaseIndex: JSON形式の抽出データ
```

<div class="page"/>

## カートへ追加

```mermaid
sequenceDiagram
autonumber

participant ItemsIndex as 商品一覧Vue
participant PurchaseIndex as 商品詳細Vue
participant webphp as web.php
participant ItemsPurchaseController as 商品購入Controller
participant PurchaseModel as 商品購入Model
participant MySQL as MySQL


PurchaseIndex ->>+ webphp:カートへ追加 押下 store()
webphp ->>+ ItemsPurchaseController: ルーティング
ItemsPurchaseController ->>+ PurchaseModel: create()
PurchaseModel ->>+ MySQL: インスタンス作成
MySQL -->>- PurchaseModel: インスタンス
PurchaseModel -->>- ItemsPurchaseController: インスタンス
ItemsPurchaseController -->>- webphp: return()
webphp -->>- ItemsIndex: JSON形式の抽出データ
```

<div class="page"/>

## 商品購入

```mermaid
sequenceDiagram
autonumber

participant ItemsIndex as 商品一覧Vue
participant ItemsShowall as カートVue
participant webphp as web.php
participant ItemsPurchaseController as 商品購入Controller
participant ItemsModel as 商品購入Model
participant MySQL as MySQL

ItemsShowall ->>+ webphp : 購入する 押下 update()
webphp ->>+ ItemsPurchaseController: ルーティング
ItemsPurchaseController ->>+ ItemsModel: save("購入状態1")
ItemsModel ->>+ MySQL: UPDATE
MySQL -->>- ItemsModel: 成功可否
ItemsModel -->>- ItemsPurchaseController: 成功可否
ItemsPurchaseController -->>- webphp: return()
webphp -->> ItemsIndex: 商品一覧Vue呼出
```

<div class="page"/>

## 購入履歴

```mermaid
sequenceDiagram
autonumber

participant PurchaseIndex as 購入履歴Vue
participant webphp as web.php
participant ItemsController as 商品購入Controller
participant ItemsModel as 商品購入Model
participant MySQL as MySQL


PurchaseIndex ->>+ webphp:初期表示 index()
webphp ->>+ ItemsController: ルーティング
ItemsController ->>+ ItemsModel: select("セッションID","購入状態1")
ItemsModel ->>+ MySQL: 条件付SELECT発行
MySQL -->>- ItemsModel: 抽出データ
ItemsModel -->>- ItemsController: 抽出データ
ItemsController -->>- webphp: return()
webphp -->>- PurchaseIndex: JSON形式の抽出データ
```
