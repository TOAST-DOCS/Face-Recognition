## AI Service > Face Recognition > Release Notes

## March 28, 2023
#### Added Features
* [API] [얼굴 스푸핑 감지](./api-guide/#spoofing) API 추가
* [API] 이미지를 사용하는 API들에 얼굴 스푸핑 감지 및 마스크 착용 여부, 얼굴 방향 감지 옵션 추가
    * [Request of Face Recognize](./api-guide/#detect-face-request)
    * [Request of Face Register](./api-guide/#add-face-request)
    * [Request of Face Compare](./api-guide/#compare-face-request)
    * [Request of Face Verification](./api-guide/#verify-request)
    * [이미지로 얼굴 검색 요청](./api-guide/#search-by-image-request)

### December 27, 2022
#### Added Features
* [API] Added whether to wear a mask to FaceDetails in response body.
    * [Response of Face Recognize](./api-guide/#detect-face-response)
    * [Response of Face Register](./api-guide/#add-face-response)
    * [Response of Face Compare](./api-guide/#compare-face-response)

### March 29, 2022
#### Feature Updates
* Applied a new model with enhancements including the improved face recognition rate
    * The new model will be automatically applied to newly created groups from March 29, 2022
    * Groups that have already been created will use the existing model as it is.
    * To use the new model, you need to create a new group and register faces again.

### July 27, 2021
#### Added Features
* [API] Added [Face Verification](./api-guide/#face-verification) API

### April 27, 2021
#### Feature Updates
* [API] Diversified input image formats

### March 23, 2021
* Released the Face Recognition Service
