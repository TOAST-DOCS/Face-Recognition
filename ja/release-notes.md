<!-- pre-align:aligned sig=d9440986b5bc -->

<a id="ai-service-face-recognition-release-notes"></a>
## AI Service > Face Recognition > リリースノート { #ai-service-face-recognition-release-notes }

<a id="march-10-2026"></a>
### 2026. 03. 10. { #march-10-2026 }

<!-- pre-align: ko에 대응 섹션 없음 — 검토 필요 (t3 is labeled 機能追加 (feature addition) but k3 is 기능 개선 (feature improvement); the heading text is semantically wrong — treated as mistranslation requiring correction, matched above, so no separate extra entry needed) -->
<a id="march-10-2026-added-features"></a>
#### 機能追加

* API v2.1 リリース
    * User Access Keyトークン認証を使用するAPIが追加されました。
    * User Access Keyトークンの発行及び使用に関する詳細は、[User Access Keyトークン](/nhncloud/ja/public-api/user-access-key-token)を参照してください。

<a id="march-11-2025"></a>
### 2025. 03. 11. { #march-11-2025 }

<a id="march-11-2025-feature-updates"></a>
#### 機能改善

* 顔検索速度が従来より5～10%向上

<a id="september-10-2024"></a>
### 2024. 09. 10. { #september-10-2024 }

<a id="september-10-2024-added-features"></a>
#### 機能追加

* [API v2.0] [グループ内の顔リスト](./api-guide-v2.0/#face-list-in-a-group)照会時、フェイスID、画像ID、ユーザーが顔登録時に設定した画像またはフェイスIDラベリング値(ExternalImageId)を利用して顔リストをフィルタリングできるように機能追加

<a id="january-09-2024"></a>
### 2024. 01. 09. { #january-09-2024 }

<a id="january-09-2024-updates"></a>
#### 機能改善

* エラーメッセージの改善(`InvalidImageParameterException` -> `InvalidImageBytesException`)

<a id="october-31-2023"></a>
### 2023. 10. 31. { #october-31-2023 }

<a id="october-31-2023-added-features"></a>
#### 機能追加

* API v2.0リリース

<a id="december-27-2022"></a>
### 2022. 12. 27. { #december-27-2022 }

<a id="december-27-2022-added-features"></a>
#### 機能追加

* [API] 応答のFaceDetailsにマスク着用の有無を追加
    * [顔検出の応答](./api-guide-v1.0/#detect-face-response)
    * [顔登録の応答](./api-guide-v1.0/#add-face-response)
    * [顔比較の応答](./api-guide-v1.0/#compare-face-response)

<a id="march-29-2022"></a>
### 2022. 03. 29. { #march-29-2022 }

<a id="march-29-2022-feature-updates"></a>
#### 機能改善

* 顔認識率などが改善された新規モデルを適用
    * 2022年03月29日から新規で作成されるグループに自動適用
    * 作成済みのグループは既存モデルをそのまま使用
    * 新規モデルを使用するにはグループを新しく作成して顔をもう一度登録する必要があります

<a id="july-27-2021"></a>
### 2021. 07. 27. { #july-27-2021 }

<a id="july-27-2021-added-features"></a>
#### 機能追加

* [API] [顔検証](./api-guide-v1.0/#verify) APIを追加

<a id="april-27-2021"></a>
### 2021.04.27. { #april-27-2021 }

<a id="april-27-2021-feature-updates"></a>
#### 機能改善

* [API]アップロード可能な画像フォーマットの多様化

<a id="march-23-2021"></a>
### 2021.03.23. { #march-23-2021 }

* Face Recognitionサービスリリース
