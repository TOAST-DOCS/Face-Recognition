## AI Service > Face Recognition > 릴리스 노트

### 2024. 09. 10.

#### 기능 추가

* [API v2.0] [그룹 내 얼굴 목록](./api-guide-v2.0/#face-list-in-a-group) 조회 시 페이스 아이디, 이미지 아이디, 사용자가 이미지에 정의한 아이디(ExternalImageId)를 이용하여 얼굴 목록을 필터링할 수 있도록 기능 추가

### 2024. 01. 09.

#### 기능 개선

* 오류 메시지 개선(`InvalidImageParameterException` -> `InvalidImageBytesException`)

### 2023. 10. 31.

#### 기능 추가

* API v2.0 출시

### 2022. 12. 27.

#### 기능 추가

* [API] 응답 본문의 FaceDetails에 마스크 착용 여부 추가
    * [얼굴 감지의 응답](./api-guide-v1.0/#detect-face-response)
    * [얼굴 등록의 응답](./api-guide-v1.0/#add-face-response)
    * [얼굴 비교의 응답](./api-guide-v1.0/#compare-face-response)

### 2022. 03. 29.

#### 기능 개선

* 얼굴 인식률 등이 개선된 신규 모델 적용
    * 2022년 03월 29일부터 신규로 생성되는 그룹들에 자동 적용
    * 이미 생성된 그룹들은 기존 모델을 그대로 사용
    * 신규 모델 사용을 위해서는 그룹을 새로 만들어서 얼굴들을 다시 등록해야 함

### 2021. 07. 27.

#### 기능 추가

* [API] [얼굴 검증](./api-guide-v1.0/#verify) API 추가

### 2021. 04. 27.

#### 기능 개선

* [API] 입력 가능한 이미지 포맷 확대

### 2021. 03. 23.

* Face Recognition 서비스 출시
