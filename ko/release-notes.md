## AI Service > Face Recognition > 릴리스 노트

### 2021.04.27
  #### 버그 수정
  * [Console] [얼굴비교] threshold 값 변경 오류 수정
    * 현상 : 키보드의 backspace로 값을 삭제하려고 하면, 최소값인 1이 고정되어 있어서 다른 숫자 입력이 불가한 이슈
    * 해결 : 최소값 1도 지울 수 있도록 수정

  * [Console] Console 에서의 요청에 대한 CloudTrail 로그 수정
    * 현상 : Console 에서 얼굴인식/얼굴비교 기능 사용했을 경우, CloudTrail 에 API 로 기록이 남는 이슈
    * 해결 : ADMIN_CONSOLE 로 기록이 남도록 수정

  * [API] [얼굴비교] threshold 값 100 설정 후 같은 이미지로 비교시 unmatched 로 구분되는 오류 수정
    * 현상 : 동일한 이미지로 얼굴비교를 할 때, threshold 를 100으로 설정하면, unmatched 로 구분되는 이슈
    * 해결 : 정상적으로 matched 로 구분되도록 수정

  * [API] [공통] jpeg 이미지 파일인데, 포맷이 잘못 되었다는 에러를 리턴하는 오류 수정
    * 현상 : 특정 jpeg 이미지 파일을 사용하는 경우, 포맷 오류가 발생하는 이슈
    * 해결 : 이미지 포맷 정상적으로 인식할 수 있도록 수정


### 2021.03.23

- Face Recognition 서비스 출시
