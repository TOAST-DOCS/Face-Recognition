## AI Service > Face Recognition > 概要

* NHN Cloudの顔認識サービスは、マシンラーニング技術で良質のデータセットを学習して開発されたサービスです。
* 顔検出および分析、比較など、多くの機能を提供し、さまざまなシナリオで身元認証機能をサポートします。
* 金融や医療、コマースなど、顔の識別が可能なオンライン/オフライン環境で細かくパーソナライズされた顧客サービスを提供できます。

## 主な機能

次のような機能を提供します。

* **Face Detection & Analysis(顔検出および分析)**
    * 入力画像から顔を検出する機能です。
    * 検出した顔のプロパティを分析して顔、目、鼻、口の位置情報と信頼度値を返します。
* **Face Comparison(顔比較)**
    * 入力画像から検出した顔が、比較対象画像から検出した顔と一致するかを比較する機能です。
    * 入力画像に複数の顔が含まれている場合、検出した顔のうち最も大きい顔と、比較対象画像から検出したそれぞれの顔を比較して類似度値を返します。
* **Managing Group(グループ管理)**
    * 顔を登録するためのグループを作成できます。作成したグループリストの照会や削除を行うことができる機能です。
    * 入力画像から検出した顔をグループに登録したり、グループに登録した顔を照会および削除できます。
* **Face Indexing & Searching(顔の検索)**
    * 事前に顔情報を分析して保存しておき、Face IDや画像を伝達して顔を検索し、身元を識別する機能です。
    * 分析した顔情報はFaceI IDで管理します。
    * この時、入力した値(Face IDや画像)と一致する一連の顔を検索結果として返し、類似度値が高い順にソートします。
* **Face Verification(顔検証)**
    * 事前に登録された特定の顔のフェイスIDと、入力画像から検出した顔を比較して類似度値を返す機能です。
    * 同じ顔なのかを判断する類似度基準値をユーザーが直接設定して一致しているかどうかを判断できます。
    * 高い水準のセキュリティが要求される場合は、類似度基準値を高くして厳密な検証を行うことができます。
* **Face Spoofing Detection(顔スプーフィング検出)**
    * 顔認識への違法なアクセスやスプーフィング(顔写真、動画などの顔認識歪曲行為)を防止するための検出機能です。
    * 検出された顔画像を分析して顔のスプーフィング有無を返します。
    * Face Recognition API(顔検出/顔登録/顔比較/画像で顔検索/顔検証)でも顔スプーフィング検出機能を使用できます。

## サービス対象

* 顔認識技術を活用した出入管理システムの構築が必要な場合
* 訪問客のモニタリングが必要な場合
* 建設現場で働く労働者の位置を確認して安全な現場環境を構築したい場合
* 顔認識技術を活用した決済システムの構築が必要な場合
* 写真アルバムから特定の顔が含まれる写真を探したい場合
* 顔画像のスプーフィング有無を把握したい場合

## 個人情報処理についての案内

* Face Recognitionサービスを利用する過程で、お客様は利用者の個人情報およびデリケートな情報を収集する場合があります。したがってこのサービスを利用するお客様は、個人情報保護法に基づいて利用者に法的告知事項を伝え、同意を得る必要があります。<br/>
また、この過程でお客様とNHN Cloudの間に個人情報処理に関する業務委受託関係が発生する場合があります。委託者であるお客様は受託社NHN Cloudと別途書面による委託契約を締結する場合があり、下記の内容を参考にしてお客様が運営する個人情報処理方針に告知できます。
    * 受託業者：NHN Cloud(株)
    * 委託業務の内容：Face Recognitionサービス提供業務
