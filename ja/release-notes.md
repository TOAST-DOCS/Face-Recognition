## AI Service > Face Recognition > リリースノート

### 2023. 03. 28.
#### 機能追加
* [API] [顔スプーフィング検出](./api-guide/#spoofing) API追加
* [API]画像を使用するAPIに顔スプーフィング検出およびマスクの着用有無、顔の方向検出オプションを追加
    * [顔検出リクエスト](./api-guide/#detect-face-request)
    * [顔登録リクエスト](./api-guide/#add-face-request)
    * [顔比較リクエスト](./api-guide/#compare-face-request)
    * [顔検証リクエスト](./api-guide/#verify-request)
    * [画像で顔検索リクエスト](./api-guide/#search-by-image-request)

### 2022. 12. 27.
#### 機能追加
* [API] 応答のFaceDetailsにマスク着用の有無を追加
    * [顔検出の応答](./api-guide/#detect-face-response)
    * [顔登録の応答](./api-guide/#add-face-response)
    * [顔比較の応答](./api-guide/#compare-face-response)

### 2022. 03. 29.
#### 機能改善
* 顔認識率などが改善された新規モデルを適用
    * 2022年03月29日から新規で作成されるグループに自動適用
    * 作成済みのグループは既存モデルをそのまま使用
    * 新規モデルを使用するにはグループを新しく作成して顔をもう一度登録する必要があります

### 2021. 07. 27.
#### 機能追加
* [API] [顔検証](./api-guide/#verify) APIを追加

### 2021.04.27.
#### 機能改善
* [API]アップロード可能な画像フォーマットの多様化
### 2021.03.23.
* Face Recognitionサービスリリース
