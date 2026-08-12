<!-- pre-align:aligned sig=52d27cbf2013 -->

<a id="ai-service-face-recognition-console-user-guide"></a>
## AI Service > Face Recognition > 콘솔 사용 가이드 { #ai-service-face-recognition-console-user-guide }

콘솔에서는 얼굴 감지 및 분석, 얼굴 비교 기능을 테스트할 수 있습니다.

콘솔을 사용한 테스트 방법은 다음과 같습니다.

<a id="recognize-and-analyze-faces"></a>
## 얼굴 감지 및 분석 { #recognize-and-analyze-faces }

<a id="photo-upload-for-face-recognition-and-analysis"></a>
### 얼굴 감지 및 분석을 위한 사진 업로드 { #photo-upload-for-face-recognition-and-analysis }
사진은 다음 3가지 방법으로 업로드할 수 있습니다.
1. 이미지 업로드 버튼 클릭
2. 이미지 URL 입력
3. 이미지 드래그 앤드 드롭

<a id="analyze"></a>
### 분석 { #analyze }
사진을 업로드한 후 분석 버튼을 클릭하면 분석 결과가 화면 오른쪽에 나타납니다.

![detect](http://static.toastoven.net/prod_facerecognition/FR_detect_kr_v2.png)

* [Result] 감지한 얼굴 영역과 신뢰도를 표시합니다.
* [Response] API 호출 시 응답되는 JSON 예시를 표시합니다.


<a id="compare-faces"></a>
## 얼굴 비교 { #compare-faces }

<a id="photo-upload-for-comparing-faces"></a>
### 얼굴 비교를 위한 사진 업로드 { #photo-upload-for-comparing-faces }
사진은 다음 3가지 방법으로 업로드할 수 있으며 기준 이미지(Reference Image)와 비교 이미지(Comparison Image) 모두 선택하여야 합니다.
1. 이미지 업로드 버튼 클릭
2. 이미지 URL 입력
3. 이미지 드래그 앤드 드롭

<a id="threshold-settings"></a>
### Threshold 설정 { #threshold-settings }
임곗값(Threshold)이란 기준 이미지에서 감지된 얼굴과 비교 이미지에서 감지된 얼굴이 일치하는지 판단하는 유사도 기준값입니다.

<a id="compare"></a>
### 비교 { #compare }
사진 업로드한 후 비교 버튼을 클릭하면 비교 결과가 화면 오른쪽에 나타납니다.

![compare](http://static.toastoven.net/prod_facerecognition/FR_compare_kr_v2.png)

* [Result] 기준 이미지(Reference Image)에서 감지한 얼굴과 비교 이미지(Comparison Image)에서 감지한 얼굴의 비교 결과와 유사도를 표시합니다. 
* [Response] API 호출 시 응답되는 JSON 예시를 표시합니다.
