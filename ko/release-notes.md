## AI Service > Face Recognition > 릴리스 노트

### 2023. 03. 28.
#### 기능 추가
* [API] [얼굴 스푸핑 감지](./api-guide/#spoofing) API 추가
* [API] 이미지를 사용하는 API들에 얼굴 스푸핑 감지 및 마스크 착용 여부, 얼굴 방향 감지 옵션 추가
    * [얼굴 감지 요청](./api-guide/#detect-face-request)
    * [얼굴 등록 요청](./api-guide/#add-face-request)
    * [얼굴 비교 요청](./api-guide/#compare-face-request)
    * [얼굴 검증 요청](./api-guide/#verify-request)
    * [이미지로 얼굴 검색 요청](./api-guide/#search-by-image-request)

### 2022. 12. 27.
#### 기능 추가
* [API] 응답 본문의 FaceDetails에 마스크 착용 여부 추가.
    * [얼굴 감지의 응답](./api-guide/#detect-face-response)
    * [얼굴 등록의 응답](./api-guide/#add-face-response)
    * [얼굴 비교의 응답](./api-guide/#compare-face-response)

### 2022. 03. 29.
#### 기능 개선
* 얼굴 인식률 등이 개선된 신규 모델 적용
    * 2022년 03월 29일부터 신규로 생성되는 그룹들에 자동 적용
    * 이미 생성된 그룹들은 기존 모델을 그대로 사용
    * 신규 모델 사용을 위해서는 그룹을 새로 만들어서 얼굴들을 다시 등록해야 함

### 2021. 07. 27.
#### 기능 추가
* [API] [얼굴 검증](./api-guide/#verify) API 추가

### 2021. 04. 27.
#### 기능 개선
* [API] 입력 가능한 이미지 포맷 확대

### 2021. 03. 23.
* Face Recognition 서비스 출시
