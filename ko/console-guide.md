## AI Service > Face Recognition > 콘솔 사용 가이드

콘솔에서는 얼굴 감지 및 분석, 얼굴 비교 기능의 DEMO 테스트를 사용 할 수 있습니다.

콘솔을 사용한 테스트 방법은 다음과 같습니다.

## 얼굴 감지 및 분석

### 얼굴 감지 및 분석을 위한 사진 선택
사진은 아래 세가지 방법으로 선택 가능합니다.
1. 이미지 업로드 버튼을 클릭하여 사진 업로드
2. 이미지 URL 입력
3. 사진을 Drag&Drop

### 분석
사진 업로드 후 분석 버튼을 클릭하면 
분석결과가 화면 우측에 출력 됩니다.

![detect](http://static.toastoven.net/prod_facerecognition/detect.png)

* [Result] 얼굴이 감지 된 영역과 신뢰도를 표시합니다.
* [Request] API 사용 시 요청되는 request json 예시가 표시 됩니다.
* [Response] API 사용 시 응답 resonse json 예시가 표시 됩니다.


## 얼굴 비교

### 얼굴 비교를 위한 사진 선택
사진은 아래 세가지 방법으로 선택 가능하며 Source Image와 Target Image 모두 선택 하여야 합니다.
1. 이미지 업로드 버튼을 클릭하여 사진 업로드
2. 이미지 URL 입력
3. 사진을 Drag&Drop

### Threshold(임계값) 설정
Threshold란 Source Image에서 감지된 얼굴과 Target Image에서 감지된 얼굴이 
일치하는지 판단하는 최소 신뢰 수준입니다.

### 비교
사진 업로드 후 비교 버튼을 클릭하면 
분석결과가 화면 우측에 출력 됩니다.

![compare](http://static.toastoven.net/prod_facerecognition/compare.png)

* [Result] Source Image에서 검출 된 얼굴과 Target Image에서 검출된 얼굴들의 비교 결과와 유사도를 표시합니다. 
* [Request] API 사용 시 요청되는 request json 예시가 표시 됩니다.
* [Response] API 사용 시 응답 resonse json 예시가 표시 됩니다.

## 제한사항
 - 이미지 최소 사이즈는 가로 세로 모두 80px 이상입니다. (얼굴크기는 최소 60X60 px 이상)
 - 이미지 파일의 최대 사이즈는 5MB 입니다.
 
## 권장사항
권장사항은 아래와 같습니다.

 - 눈썹이 명확히 보이는 이미지 사용
 - 카메라 기준 얼굴이 정면을 바라본 이미지 사용
 - 두 눈이 모두 보이며 눈을 뜨고 있는 이미지 사용
 - 가려지거나 잘리지 않은 얼굴 이미지 사용
 - 머리띠나 마스크와 같이 얼굴을 가리는 물건을 착용하지 않은 이미지 사용
 - 컬러 이미지 사용
 - 단일한 조명이 비추는 이미지 사용
 - 배경과 충분히 대비가 있는 이미지 사용
 - 밝고 선명한 이미지 사용
