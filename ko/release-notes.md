## AI Service > Face Recognition > 릴리스 노트

### 2021. 04. 27.
#### 버그 수정
* [Console] [얼굴 비교] threshold 값 변경 오류 수정
  * 현상: 키보드의 backspace로 값을 삭제하려고 하면 최솟값인 1로 고정되어 있어서 다른 숫자 입력이 불가한 이슈
  * 해결: 최솟값 1을 삭제할 수 있도록 수정

* [Console] [CloudTrail] Console에서 인입된 요청에 대한 CloudTrail 로그 수정
  * 현상: Console에서 얼굴 감지 및 분석/얼굴 비교 기능을 사용할 경우 CloudTrail의 소스 로그에 'API'로 로깅되는 이슈
  * 해결: 'ADMIN_CONSOLE'로 로깅되도록 수정

* [API] [얼굴 비교] threshold 값을 100으로 설정한 후 동일한 이미지로 비교 시 unmatched로 구분되는 오류 수정
  * 현상: 동일한 이미지로 얼굴 비교를 할 때, threshold 값을 100으로 설정하면, unmatched로 구분되는 이슈
  * 해결: matched로 구분되도록 수정

* [API] [공통] JPEG 이미지 파일 입력 시 포맷이 잘못 되었다는 에러를 반환하는 오류 수정
  * 현상: 특정 JPEG 이미지 파일 입력 시 포맷 오류가 발생하는 이슈
  * 해결: JPEG 이미지 파일을 정상적으로 인식할 수 있도록 수정


### 2021. 03. 23.
* Face Recognition 서비스 출시
