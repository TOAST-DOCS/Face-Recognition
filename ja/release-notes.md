## AI Service > Face Recognition > リリースノート

### 2025. 03. 11.

#### 機能改善

* 顔検索速度が従来より5～10%向上

### 2024. 09. 10.

#### 機能追加

* [API v2.0] [グループ内の顔リスト](./api-guide-v2.0/#face-list-in-a-group)照会時、フェイスID、画像ID、ユーザーが顔登録時に設定した画像またはフェイスIDラベリング値(ExternalImageId)を利用して顔リストをフィルタリングできるように機能追加

### 2024. 01. 09.

#### 機能改善

* エラーメッセージの改善(`InvalidImageParameterException` -> `InvalidImageBytesException`)

### 2023. 10. 31.

#### 機能追加

* API v2.0リリース

### 2022. 12. 27.

#### 機能追加

* [API] 応答のFaceDetailsにマスク着用の有無を追加
    * [顔検出の応答](./api-guide-v1.0/#detect-face-response)
    * [顔登録の応答](./api-guide-v1.0/#add-face-response)
    * [顔比較の応答](./api-guide-v1.0/#compare-face-response)

### 2022. 03. 29.

#### 機能改善

* 顔認識率などが改善された新規モデルを適用
    * 2022年03月29日から新規で作成されるグループに自動適用
    * 作成済みのグループは既存モデルをそのまま使用
    * 新規モデルを使用するにはグループを新しく作成して顔をもう一度登録する必要があります

### 2021. 07. 27.

#### 機能追加

* [API] [顔検証](./api-guide-v1.0/#verify) APIを追加

### 2021.04.27.

#### 機能改善

* [API]アップロード可能な画像フォーマットの多様化

### 2021.03.23.

* Face Recognitionサービスリリース
