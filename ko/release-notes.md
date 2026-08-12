<!-- pre-align:aligned sig=d9440986b5bc -->

<a id="ai-service-face-recognition-release-notes"></a>
## AI Service > Face Recognition > 릴리스 노트 { #ai-service-face-recognition-release-notes }

<a id="march-10-2026"></a>
### 2026. 03. 10. { #march-10-2026 }

<a id="march-10-2026-added-features"></a>
#### 기능 개선

* API v2.1 출시
    * User Access Key 토큰 인증을 사용하는 API가 추가되었습니다.
    * User Access Key 토큰 발급 및 사용에 대한 자세한 내용은 [User Access Key 토큰](/nhncloud/ko/public-api/user-access-key-token)을 참고하세요.

<a id="march-11-2025"></a>
### 2025. 03. 11. { #march-11-2025 }

<a id="march-11-2025-feature-updates"></a>
#### 기능 개선

* 얼굴 검색 속도 기존 대비 5~10% 향상

<a id="september-10-2024"></a>
### 2024. 09. 10. { #september-10-2024 }

<a id="september-10-2024-added-features"></a>
#### 기능 추가

* [API v2.0] [그룹 내 얼굴 목록](./api-guide-v2.0/#list-of-faces-within-group) 조회 시 페이스 아이디, 이미지 아이디, 사용자가 얼굴 등록 시 설정한 이미지 또는 페이스 아이디 라벨링 값(ExternalImageId)을 이용하여 얼굴 목록을 필터링할 수 있도록 기능 추가

<a id="january-09-2024"></a>
### 2024. 01. 09. { #january-09-2024 }

<a id="january-09-2024-updates"></a>
#### 기능 개선

* 오류 메시지 개선(`InvalidImageParameterException` -> `InvalidImageBytesException`)

<a id="october-31-2023"></a>
### 2023. 10. 31. { #october-31-2023 }

<a id="october-31-2023-added-features"></a>
#### 기능 추가

* API v2.0 출시

<a id="december-27-2022"></a>
### 2022. 12. 27. { #december-27-2022 }

<a id="december-27-2022-added-features"></a>
#### 기능 추가

* [API] 응답 본문의 FaceDetails에 마스크 착용 여부 추가
    * [얼굴 감지의 응답](./api-guide-v1.0/#recognize-face-response)
    * [얼굴 등록의 응답](./api-guide-v1.0/#register-face-response)
    * [얼굴 비교의 응답](./api-guide-v1.0/#compare-faces-response)

<a id="march-29-2022"></a>
### 2022. 03. 29. { #march-29-2022 }

<a id="march-29-2022-feature-updates"></a>
#### 기능 개선

* 얼굴 인식률 등이 개선된 신규 모델 적용
    * 2022년 03월 29일부터 신규로 생성되는 그룹들에 자동 적용
    * 이미 생성된 그룹들은 기존 모델을 그대로 사용
    * 신규 모델 사용을 위해서는 그룹을 새로 만들어서 얼굴들을 다시 등록해야 함

<a id="july-27-2021"></a>
### 2021. 07. 27. { #july-27-2021 }

<a id="july-27-2021-added-features"></a>
#### 기능 추가

* [API] [얼굴 검증](./api-guide-v1.0/#face-verification) API 추가

<a id="april-27-2021"></a>
### 2021. 04. 27. { #april-27-2021 }

<a id="april-27-2021-feature-updates"></a>
#### 기능 개선

* [API] 입력 가능한 이미지 포맷 확대

<a id="march-23-2021"></a>
### 2021. 03. 23. { #march-23-2021 }

* Face Recognition 서비스 출시
