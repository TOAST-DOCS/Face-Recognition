## AI Service > Face Recognition > コンソール使用ガイド

コンソールでは顔の検出および分析、顔の比較機能をテストできます。

コンソールを使用したテスト方法は次のとおりです。

## 顔の検出および分析

### 顔の検出および分析を行うための事前アップロード
画像は次の3つの方法でアップロードできます。
1. 画像アップロードボタンをクリック
2. 画像URLを入力
3. 画像をドラッグアンドドロップ

### 分析
画像をアップロードして分析ボタンを押すと、分析結果が画面右側に表示されます。

![detect](http://static.toastoven.net/prod_facerecognition/FR_detect_jp_v2.png)

* [Result]検出した顔の領域と信頼度を表示します。
* [Response] APIの呼び出し時にレスポンスされるJSONサンプルを表示します。


## 顔の比較

### 顔を比較するための画像アップロード
画像は次の3つの方法でアップロードできます。基準画像(Reference Image)と比較画像(Comparison Image)はどちらも選択する必要があります。
1. 画像アップロードボタンをクリック
2. 画像URLを入力
3. 画像をドラッグアンドドロップ

### Threshold設定
しきい値(Threshold)とは、基準画像から検出された顔と比較画像から検出された顔が一致するかを判断する類似度の基準値です。

### 比較
画像をアップロードして比較ボタンを押すと、比較結果が画面右側に表示されます。

![compare](http://static.toastoven.net/prod_facerecognition/FR_compare_jp_v2.png)

* [Result]基準画像(Reference Image)から検出した顔と比較画像(Comparison Image)から検出した顔の比較結果と類似度を表示します。 
* [Response] APIの呼び出し時にレスポンスされるJSONサンプルを表示します。
