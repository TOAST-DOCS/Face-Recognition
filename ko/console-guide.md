## AI Service > Face Recognition > 콘솔 사용 가이드

콘솔에서는 얼굴 감지 및 분석, 얼굴 비교 기능을 테스트할 수 있습니다.

콘솔을 사용한 테스트 방법은 다음과 같습니다.

## 얼굴 감지 및 분석

### 얼굴 감지 및 분석을 위한 사진 업로드
사진은 아래 세 가지 방법으로 업로드 가능합니다.
1. 이미지 업로드 버튼을 클릭하여 업로드
2. 이미지 URL을 입력하여 업로드
3. 이미지를 Drag&Drop 하여 업로드

### 분석
사진 업로드 후 분석 버튼을 클릭하면 
분석 결과가 화면 우측에 출력 됩니다.

![detect](http://static.toastoven.net/prod_facerecognition/detect.png)

* [Result] 감지한 얼굴 영역과 신뢰도를 표시합니다.
* [Request] API 호출 시 요청하는 json 예시를 표시합니다.
* [Response] API 호출 시 응답되는 json 예시를 표시합니다.


## 얼굴 비교

### 얼굴 비교를 위한 사진 업로드
사진은 아래 세 가지 방법으로 업로드 가능하며 기준 이미지(Reference Image)와 비교 이미지(Comparison Image) 모두 선택하여야 합니다.
1. 이미지 업로드 버튼을 클릭하여 업로드
2. 이미지 URL을 입력하여 업로드
3. 이미지를 Drag&Drop 하여 업로드

### Threshold 설정
Threshold란 기준 이미지(Reference Image)에서 감지한 얼굴과 비교 이미지(Comparison Image)에서 감지한 얼굴이 
일치하는지 판단하는 최소 신뢰 수준입니다.

### 비교
사진 업로드 후 비교 버튼을 클릭하면 
분석 결과가 화면 우측에 출력 됩니다.

![compare](http://static.toastoven.net/prod_facerecognition/compare.png)

* [Result] 기준 이미지(Reference Image)에서 감지한 얼굴과 비교 이미지(Comparison Image)에서 감지한 얼굴의 비교 결과와 유사도를 표시합니다. 
* [Request] API 호출 시 요청하는 json 예시를 표시합니다.
* [Response] API 호출 시 응답되는 json 예시를 표시합니다.
