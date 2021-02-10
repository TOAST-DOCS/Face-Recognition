# AI Service > Face Recognition > 얼굴인식 API 가이드

* 얼굴인식 API를 사용하는 데 필요한 API를 설명합니다.

## API 공통 정보
### 사전 준비
- API 사용을 위해서는 앱 키와 보안 키가 필요합니다.
- 앱 키와 보안 키는 콘솔 상단 "URL & Appkey" 메뉴에서 확인이 가능합니다.
### 요청 공통 정보

- API를 사용하기 위해서는 보안 키 인증 처리가 필요합니다.
- 모든 API 요청 헤더에 'Authorization'에 보안 키를 넣어서 요청해야 합니다.

[API 도메인]

| 환경 | 도메인 |
| --- | --- |
| Alpha | https://alpha-face-recognition.cloud.toast.com |

[요청 헤더]

| 이름 | 값 | 설명 |
|---|---|---|
| Authorization | {secretKey} | 콘솔에서 발급받은 보안 키 |

### 응답 공통 정보

- 모든 API 요청에 "200 OK"로 응답합니다. 자세한 응답 결과는 응답 본문 헤더를 참고합니다.

[응답 본문 헤더]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | boolean | true: 정상<br>false: 오류 |
| header.resultCode | int | 0: 정상<br>음수: 오류 |
| header.resultMessage | string | "SUCCESS": 정상<br>그 외: 오류 메시지 리턴 |


[성공 응답 본문 예]

```json
{
	"header": {
		"isSuccessful": true,
		"resultCode": 0,
		"resultMessage": "Success"
	}
}
```

[실패 응답 본문시 예]

```json
{
	"header": {
		"isSuccessful": false,
		"resultCode": -40000,
		"resultMessage": "InvalidParam"
	}
}
```
## API 목차

1. [그룹 생성](#그룹-생성)
2. [그룹 목록](#그룹-목록)
3. [그룹 디테일](#그룹-디테일)
4. [그룹 삭제](#그룹-삭제)
5. [얼굴 감지](#얼굴-감지)
6. [얼굴 추가](#얼굴-추가)
7. [얼굴 삭제](#얼굴-삭제)
8. [그룹 내 얼굴 목록](#그룹-내-얼굴-목록)
9. [얼굴 아이디로 검색](#얼굴-아이디로-검색)
10. [이미지로 검색](#이미지로-검색)
11. [얼굴 비교](#얼굴-비교)


### 그룹 생성

* 그룹 생성을 하는 API입니다. 생성된 그룹에 "[얼굴 추가](#얼굴-추가)"를 이용하여 얼굴들을 등록할 수 있습니다.
#### 요청
[URI]

| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |

[Request Body]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| groupId | string | O | "my-group" | 사용자가 등록한 group id<br>[a-z0-9-]{1,255} |



[요청 예]
```sh
curl -X POST 'https://alpha-face-recognition.cloud.toast.com/nhn-face-reco/v1.0/appkeys/{appKey}/groups' \
 -H 'Authorization: {secretKey}' \
 -H 'Content-Type: application/json;charset=UTF-8' \
 -d '{
    "groupId": "my-group"
}'
```

#### 응답

[응답 본문 예]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

#### error codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | invalid param |
| -40000 | InvalidGroupID | invalid group ID pattern |
| -40000 | DuplicatedGroupIDError | duplicated group ID |
| -41000 | UnauthorizedAppKey | unauthorized appKey |
| -50000 | InternalServerError | internal server error |

### 그룹 목록

* 그룹 목록 조회

#### 요청

[URI]
| 메서드 | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups |


[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |

[URL Parameter]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | 최대 크기<br>0 < limit <= 200 |
| next-token | string |  | "skljsdioew..." | "그룹 목록 응답 본문 data"에서 반환된 값. next-token 이후로 limit를 세어서 리턴 |


* `주의 사항`
    * 처음에는 next-token이 존재 할 수 없다.
    * token은 특정 시간이나 특정 조건에서 사라질 수 있다.
    * token 발행 시 limit는 고정된다.
* 시나리오 example)

1. 최초 query

[요청 예]
```shell script
curl -X GET '/nhn-face-reco/v1.0/appkeys/{appKey}/groups?limit={limit}' \
 -H 'Authorization: {secretKey}' \
 -H 'Content-Type: application/json;charset=UTF-8'
```

2. response 에 포함된 nextToken을 이용하여 query

[요청 예]
```shell script
curl -X GET '/nhn-face-reco/v1.0/appkeys/{appKey}/groups?limit={limit}&next-token={next-token}' \
 -H 'Authorization: {secretKey}' \
 -H 'Content-Type: application/json;charset=UTF-8'
```


* next-token이 존재하면 limit는 변경 될 수 없으며 token이 발행 될 때의 값으로 자동 세팅된다.

#### 응답

[응답 본문 예]

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "groupCount": 2,
        "groups": [{
            "groupId": "group-id",
            "modelVersion": "v1.0"
        }, {
            "groupId": "my-group",
            "modelVersion": "v1.0"
        }],
        "nextToken":"dlkj-210jwoivndslko9d..."
    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능
[응답 본문 data]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.groupCount | int | O | 2 | group count |
| data.groups[].groupId | string | O | "group-id" | 사용자가 등록한 group id |
| data.groups[].modelVersion | string | O | "v1.0" | NHN Face Recognition version 정보 |
| data.nextToken | string | O | "dlkj-210jwoivndslko9d..." | paging에서 사용할 token. request에 next-token 파라미터를 전달하면 그 이후부터 센다. |

#### error codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | internal server error |
| -40000 | InvalidParam | invalid param |
| -41000 | UnauthorizedAppKey | unauthorized appkey |
| -40000 | InvalidTokenError | using wrong token |

### 그룹 디테일

* 그룹 상세 정보 조회

#### 요청
[URI]

| 메서드 | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |
| group-id | 사용자가 등록한 group id<br>[a-z0-9-]{1,255} |

[요청 예]
```
curl -X GET '/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}' \
 -H 'Authorization: {secretKey}' \
 -H 'Content-Type: application/json;charset=UTF-8'
```

#### 응답

[응답 본문 예]

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "groupCount": 1,
        "groups": [{
            "groupId": "group-id",
            "modelVersion": "v1.0",
            "createTime": "2020-09-29T14:34:12",
            "faceCount": 365
        }]
    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.groupCount | int | true | 2 | group count |
| data.groups[].groupId | string | true | "group-id" | 사용자가 등록한 group id |
| data.groups[].modelVersion | string | true | "v1.0" | NHN Face Recognition version 정보 |
| data.groups[].createTime | string | true | "2020-11-04T12:36:24" | Group을 생성한 시간. |
| data.groups[].faceCount | int | false | 365 | 그룹에 등록된 face Id 숫자 |

#### error codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | internal server error |
| -40000 | InvalidParam | invalid param |
| -41000 | UnauthorizedAppKey | unauthorized appkey |
| -40000 | NotFoundGroupError | not found group ID |

### 그룹 삭제

* 그룹 삭제

#### 요청

[URI]

| 메서드 | URI |
| --- | --- |
| DELETE | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |
| group-id | 사용자가 등록한 group id<br>[a-z0-9-]{1,255} |

[요청 예]

```shell script
curl -X DELETE '/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}' \
 -H 'Authorization: {secretKey}' \
 -H 'Content-Type: application/json;charset=UTF-8' 
```



#### 응답

[응답 본문 예]

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

#### error codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | internal server error |
| -40000 | InvalidParam | invalid param |
| -41000 | UnauthorizedAppKey | unauthorized appkey |
| -40000 | NotFoundGroupError | not found group ID |

### 얼굴 감지

* 이미지로부터 얼굴 정보를 가지고 온다.
* detect 된 face가 큰 순서대로 최대 `100`개의 얼굴 정보를 리턴한다.

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/detect |

[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |

[요청 예]
 
```shell script
curl -X POST '/nhn-face-reco/v1.0/appkeys/{appKey}/detect' \
 -H 'Authorization: {secretKey}' \
 -H 'Content-Type: application/json;charset=UTF-8' \
 -d '{
    "image": {
        "url":"https://..."
    }
}'
```

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| image.url | string | false | "https://..." | 이미지의 url |
| image.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes |

* image.url, image.bytes 중 반드시 1개가 있어야 한다.

#### 응답

[응답 본문 예]

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "faceDetailCount": 1,
        "faceDetails": [{
            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "landmarks": [{
                    "type": "leftEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "rightEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "nose",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "leftLip",
                    "x": 0.415,
                    "y": 0.513
                }, {
                    "type": "rightLip",
                    "x": 0.415,
                    "y": 0.513
                }
            ],

            "confidence": 99.8945155187
        }]
    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.faceDetailCount | int | O | 1 | detected face 개수 |
| data.faceDetails[].bbox | object | O | - | 얼굴 detect된 bbox |
| data.faceDetails[].bbox.x0 | float | O | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.faceDetails[].bbox.y0 | float | O | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.faceDetails[].bbox.x1 | float | O | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.faceDetails[].bbox.y1 | float | O | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.faceDetails[].landmarks | array | O | - | 얼굴 특징 |
| data.faceDetails[].landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.faceDetails[].landmarks[].y | float | O | 0.362 | y 좌표 |
| data.faceDetails[].landmarks[].x | float | O | 0.362 | x 좌표 |
| data.faceDetails[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |

#### Error Codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| ImageTooLargeException | -45000 | exceed image size |
| InvalidImageFormatException | -45000 | not supported image format |
| InvalidImageURLException | -45000 | invalid image url |
| ImageTimeoutError | -45000 | image download timeout |

### 얼굴 추가

* 이미지를 통해 detect된 faces를 특정 그룹에 등록한다.

#### 요청

```
POST {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}
{
    "image": {
        "url": "https://..."
    },
    "externalImageId": "image01.jpg",
    "limit": 3
}
```

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| group id | url path | true | "my-group" | Create Group을 통해 등록된 group id<br>[a-z0-9-]{1,255} |
| image.url | string | false | "https://..." | 이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| image.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| externalImageId | string | false | "image01.jsp" | 사용자가 이미지 또는 Face ID들에 labeling을 위해 전달하는 값<br>[a-zA-Z0-9\_.-:]+<br>1 <limit <= 255 |
| limit | int | true | 3 | 전달된 이미지에서 detected faces 중 크기가 큰 순으로 정렬하여 최대 그룹에 등록할 face 개수<br>0< limit <= 100 |

#### 응답

[응답 본문 예]

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "modelVersion": "v1.0",
        "addedFaceCount": 1,
        "addedFaces": [{

            "faceId": "87db50d4-f2c6-b8ea-05ed-9f201309fd92",
            "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
            "externalImageId": "image01.jpg",
            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "confidence": 99.8945155187

        }],
        "addedFaceDetails": [{

            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "landmarks": [{
                    "type": "leftEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "rightEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "nose",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "leftLip",
                    "x": 0.415,
                    "y": 0.513
                }, {
                    "type": "rightLip",
                    "x": 0.415,
                    "y": 0.513
                }
            ],
            "confidence": 99.8945155187

        }],

        "notAddedFaceCount": 1,
        "notAddedFaces": [{

            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "landmarks": [{
                    "type": "leftEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "rightEye",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "nose",
                    "x": 0.415,
                    "y": 0.513
                },
                {
                    "type": "leftLip",
                    "x": 0.415,
                    "y": 0.513
                }, {
                    "type": "rightLip",
                    "x": 0.415,
                    "y": 0.513
                }
            ],
            "confidence": 99.8945155187

        }]

    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | true | "v1.0" | Face Model version |
| data.addedFaceCount | int | true | 1 | detected face 개수 |
| data.addedFaces[].bbox | object | true | - | 얼굴 detect된 bbox |
| data.addedFaces[].bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.addedFaces[].bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.addedFaces[].bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.addedFaces[].bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.addedFaces[].faceId | string | true | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | face id |
| data.addedFaces[].imageId | string | true | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 id. 한 이미지 id에 여러 face id가 존재 할 수 있다. |
| data.addedFaces[].externalImageId | string | false | "image01.jpg" | request parameter로 전달 된 값 |
| data.addedFaces[].confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |
| data.addedFaceDetails[].bbox | object | true | - | 얼굴 detect된 bbox |
| data.addedFaceDetails[].bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.addedFaceDetails[].bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.addedFaceDetails[].bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.addedFaceDetails[].bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.addedFaceDetails[].landmarks | array | true | - | 얼굴 특징 |
| data.addedFaceDetails[].landmarks[].type | string | true | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.addedFaceDetails[].landmarks[].y | float | true | 0.362 | y 좌표 |
| data.addedFaceDetails[].landmarks[].x | float | true | 0.362 | x 좌표 |
| data.addedFaceDetails[].confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |
| data.notAddedFaceCount | int | true | 1 | detected face 개수 |
| data.notAddedFaces[].bbox | object | true | - | 얼굴 detect된 bbox |
| data.notAddedFaces[].bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.notAddedFaces[].bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.notAddedFaces[].bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.notAddedFaces[].bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.notAddedFaces[].landmarks | array | true | - | 얼굴 특징 |
| data.notAddedFaces[].landmarks[].type | string | true | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.notAddedFaces[].landmarks[].x | float | true | 0.362 | x 좌표 |
| data.notAddedFaces[].landmarks[].y | float | true | 0.362 | y 좌표 |
| data.notAddedFaces[].confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |

#### Error Codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| NotFoundGroupError | -40000 | not found group ID |
| ImageTooLargeException | -45000 | exceed image size |
| InvalidImageFormatException | -45000 | not supported image format |
| InvalidImageURLException | -45000 | invalid image url |
| ImageTimeoutError | -45000 | image download timeout |

### 얼굴 삭제

* 등록된 face 삭제

#### 요청

```
DELETE {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}/faces/{face-id}
```

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| group-id | url path | true | "group-id" | 등록된 group id<br>[a-z0-9-]{1,255} |
| face-id | url path | true | "group-id" | 등록된 face id |

#### 응답

[응답 본문 예]

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

#### Error Codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| NotFoundGroupError | -40000 | not found group ID |
| NotFoundFaceIDError | -40000 | not found face ID |

### 그룹 내 얼굴 목록

* 특정 그룹에 등록된 face list 조회

#### 요청

```
GET {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}/faces?limit={limit}&next-token={next-token}
```

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| group id | url path | true | "my-group" | 찾으려는 그룹 group id<br>[a-z0-9-]{1,255} |
| limit | int | true | 100 | 최대 크기<br>0< limit <= 200 |
| next-token | string | false | "skljsdioew..." | Get Face List response에 존재하는 값. next-token 이후로 limit를 세어서 리턴 |

* `주의 사항`
    * 처음에는 next-token이 존재 할 수 없다.
    * token은 특정 시간이나 특정 조건에서 사라질 수 있다.
    * token 발행 시 limit는 고정된다.
* 시나리오 example)

1. 최초 query

```
GET {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}/faces?limit={limit}
```

2. response 에 포함된 nextToken을 이용하여 query

```
GET {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}/faces?next-token={next-token}&limit={limit}
```

* next-token이 존재하면 limit는 변경 될 수 없으며 token이 발행 될 때의 값으로 자동 세팅된다.

#### 응답

[응답 본문 예]

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "modelVersion": "v1.0",
        "faceCount": 2,
        "faces": [{
                "faceId": "17db50d4-f2c6-b8ea-05ed-9f201309fd92",
                "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                "externalImageId": "image01.jpg",
                "bbox": {
                    "x0": 0.36,
                    "y0": 0.21,
                    "x1": 0.612,
                    "y1": 0.715
                },
                "confidence": 99.8945155187
            }, 
            {
                "faceId": "87db50d4-f2c6-b8ea-05ed-9f201309fd92",
                "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                "externalImageId": "image01.jpg",
                "bbox": {
                    "x0": 0.36,
                    "y0": 0.21,
                    "x1": 0.612,
                    "y1": 0.715
                },
                "confidence": 99.8945155187
            }],
        "nextToken":"dlkj-210jwoivndslko9d..."
    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | true | "v1.0" | Face Model version |
| data.faceCount | int | true | 2 | detected face 개수 |
| data.faces[].bbox | object | true | - | 얼굴 detect된 bbox |
| data.faces[].bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.faces[].bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.faces[].bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.faces[].bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.faces[].confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |
| data.faces[].faceId | string | true | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | face id |
| data.faces[].imageId | string | true | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 id. 한 이미지 id에 여러 face id가 존재 할 수 있다. |
| data.faces[].externalImageId | string | false | "image01.jpg" | 사용자가 이미지에 등록한 값 |
| data.nextToken | string | true | "dlkj-210jwoivndslko9d..." | paging에서 사용할 token. request에 next-token 파라미터를 전달하면 그 이후부터 센다. |

#### Error Codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| NotFoundGroupError | -40000 | not found group ID |
| InvalidTokenError | -40000 | using wrong token |

### 얼굴 아이디로 검색

* face id로 특정 그룹에서 검색

#### 요청

```
GET {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}/faces/{face-id}?limit={limit}&threshold ={threshold }
```

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| group-id | url path | true | "group-id" | 등록된 group id<br>[a-z0-9-]{1,255} |
| face-id | url path | true | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | 비교하려는 face id |
| limit | int | true | 10 | 찾으려는 최대 값.<br>0 < limit <= 4096 |
| threshold | int | true | 90 | 유사도<br>0 < threshold <= 100 |

#### 응답

[응답 본문 예]

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "matchFaceCount": 2,
        "matchFaces": [{
                "face": {
                    "faceId": "87db50d4-f2c6-b8ea-05ed-9f201309fd92",
                    "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                    "externalImageId": "image01.jpg",
                    "bbox": {
                        "x0": 0.36,
                        "y0": 0.21,
                        "x1": 0.612,
                        "y1": 0.715
                    },
                    "confidence": 99.8945155187
                },
                "similarity": 99.8945155187
            },
            {
                "face": {
                    "faceId": "17db50d4-f2c6-b8ea-05ed-9f201309fd92",
                    "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                    "externalImageId": "image01.jpg",
                    "bbox": {
                        "x0": 0.36,
                        "y0": 0.21,
                        "x1": 0.612,
                        "y1": 0.715
                    },
                    "confidence": 99.8945155187
                },
                "similarity": 79.8945155187
            }
        ]
    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.matchFaceCount | int | true | 2 | detected face 개수 |
| data.matchFaces[].face.bbox | object | true | - | 얼굴 detect된 bbox |
| data.matchFaces[].face.bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.matchFaces[].face.bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.matchFaces[].face.bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.matchFaces[].face.bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.matchFaces[].face.confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |
| data.matchFaces[].face.faceId | string | true | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | face id |
| data.matchFaces[].face.imageId | string | true | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 id. 한 이미지 id에 여러 face id가 존재 할 수 있다. |
| data.matchFaces[].face.externalImageId | string | false | "image01.jpg" | 사용자가 이미지에 등록한 값 |
| data.matchFaces[].similarity | float | true | 98.156 | 0\~100 값을 가지는 유사도 |

#### Error Codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| NotFoundGroupError | -40000 | not found group ID |
| NotFoundFaceIDError | -40000 | not found face ID |

### 이미지로 검색

* image로 특정 그룹에서 검색
* 전달된 이미지에서 detected faces 중 가장 큰 이미지를 비교대상으로 사용한다.

#### 요청

```
POST {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}/search?limit={limit}&threshold ={threshold }
{
    "image": {
        "url": "https://..."
    }
}
```

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| group id | url path | true | "my-group" | Create Group을 통해 등록된 group id<br>[a-z0-9-]{1,255} |
| image.url | string | false | "https://..." | 이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| image.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| limit | int | true | 10 | 찾으려는 최대 값.<br>0 < limit <= 4096 |
| threshold | int | true | 90 | 유사도<br>0 < threshold <= 100 |

#### 응답

[응답 본문 예]

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "modelVersion": "v1.0",
        "matchFaceCount": 2,
        "matchFaces": [{
                "face": {
                    "faceId": "87db50d4-f2c6-b8ea-05ed-9f201309fd92",
                    "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                    "externalImageId": "image01.jpg",
                    "bbox": {
                        "x0": 0.36,
                        "y0": 0.21,
                        "x1": 0.612,
                        "y1": 0.715
                    },
                    "confidence": 99.8945155187
                },
                "similarity": 99.8945155187
            },
            {
                "face": {
                    "faceId": "17db50d4-f2c6-b8ea-05ed-9f201309fd92",
                    "imageId": "9297db50-d4f2-c6b8-ea05-edf2013089fd",
                    "externalImageId": "image01.jpg",
                    "bbox": {
                        "x0": 0.36,
                        "y0": 0.21,
                        "x1": 0.612,
                        "y1": 0.715
                    },
                    "confidence": 99.8945155187
                },
                "similarity": 79.8945155187
            }
        ],
        "sourceFace": {
            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "confidence": 99.8945155187
        }
    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.matchFaceCount | int | true | 2 | detected face 개수 |
| data.matchFaces[].face.bbox | object | true | - | 얼굴 detect된 bbox |
| data.matchFaces[].face.bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.matchFaces[].face.bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.matchFaces[].face.bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.matchFaces[].face.bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.matchFaces[].face.confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |
| data.matchFaces[].face.faceId | string | true | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | face id |
| data.matchFaces[].face.imageId | string | true | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 id. 한 이미지 id에 여러 face id가 존재 할 수 있다. |
| data.matchFaces[].face.externalImageId | string | false | "image01.jpg" | 사용자가 이미지에 등록한 값 |
| data.matchFaces[].similarity | float | true | 98.156 | 0\~100 값을 가지는 유사도 |
| data.sourceFace.bbox | object | true | - | 얼굴 detect된 bbox |
| data.sourceFace.bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.sourceFace.bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.sourceFace.bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.sourceFace.bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.sourceFace.confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |

#### Error Codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| NotFoundGroupError | -40000 | not found group ID |
| ImageTooLargeException | -45000 | exceed image size |
| InvalidImageFormatException | -45000 | not supported image format |
| InvalidImageURLException | -45000 | invalid image url |
| ImageTimeoutError | -45000 | image download timeout |

### 얼굴 비교

* 두 이미지를 비교한다.
* Source Image에 2개 이상의 face가 detect된다면 가장 큰 face를 사용한다.

#### 요청

```
POST {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/compare
{
    "sourceImage": {
        "url": "https://..."
    },
    "targetImage": {
        "url": "https://..."
    },
    "threshold ": 80
}
```

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| sourceImage | object | true | - | 주어진 이미지에서 detected faces 중 가장 큰 face를 source로 사용하여 targets과 비교한다. |
| sourceImage.url | string | false | "https://..." | <br>이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| sourceImage.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| targetImage | object | true | - | source Image와 비교할 대상 |
| targetImage.url | string | false | "https://..." | <br>이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| targetImage.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| threshold | int | true | 90 | 유사도<br>0 < threshold <= 100 |

#### 응답

[응답 본문 예]

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "modelVersion": "v1.0",
        "matchedFaceDetailCount": 2,
        "matchedFaceDetails": [{
            "faceDetail": {
                "bbox": {
                    "x0": 0.36,
                    "y0": 0.21,
                    "x1": 0.612,
                    "y1": 0.715
                },
                "landmarks": [{
                        "type": "leftEye",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "rightEye",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "nose",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "leftLip",
                        "x": 0.415,
                        "y": 0.513
                    }, {
                        "type": "rightLip",
                        "x": 0.415,
                        "y": 0.513
                    }
                ],
                "confidence": 99.8945155187
            },
            "similarity": 90.654
        }, {
            "faceDetail": {
                "bbox": {
                    "x0": 0.36,
                    "y0": 0.21,
                    "x1": 0.612,
                    "y1": 0.715
                },
                "landmarks": [{
                        "type": "leftEye",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "rightEye",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "nose",
                        "x": 0.415,
                        "y": 0.513
                    },
                    {
                        "type": "leftLip",
                        "x": 0.415,
                        "y": 0.513
                    }, {
                        "type": "rightLip",
                        "x": 0.415,
                        "y": 0.513
                    }
                ],
                "confidence": 99.8945155187
            },
            "similarity": 90.654
        }],
        "unmatchedFaceDetailCount": 1,
        "unmatchedFaceDetails": [{
                "faceDetail": {
                    "bbox": {
                        "x0": 0.36,
                        "y0": 0.21,
                        "x1": 0.612,
                        "y1": 0.715
                    },
                    "landmarks": [{
                            "type": "leftEye",
                            "x": 0.415,
                            "y": 0.513
                        },
                        {
                            "type": "rightEye",
                            "x": 0.415,
                            "y": 0.513
                        },
                        {
                            "type": "nose",
                            "x": 0.415,
                            "y": 0.513
                        },
                        {
                            "type": "leftLip",
                            "x": 0.415,
                            "y": 0.513
                        }, {
                            "type": "rightLip",
                            "x": 0.415,
                            "y": 0.513
                        }
                    ],
                    "confidence": 99.8945155187
                },
                "similarity": 60.654
            }

        ],
        "sourceFace": {
            "bbox": {
                "x0": 0.36,
                "y0": 0.21,
                "x1": 0.612,
                "y1": 0.715
            },
            "confidence": 99.8945155187
        }
    }
}
```

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | true | "v1.0" | Face Model version |
| data.matchedFaceDetailCount | int | true | 1 | matched face 개수 |
| data.matchedFaceDetails[].faceDetail.bbox | object | true | - | 얼굴 detect된 bbox |
| data.matchedFaceDetails[].faceDetail.bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.matchedFaceDetails[].faceDetail.bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.matchedFaceDetails[].faceDetail.bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.matchedFaceDetails[].faceDetail.bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.matchedFaceDetails[].faceDetail.landmarks | array | true | - | 얼굴 특징 |
| data.matchedFaceDetails[].faceDetail.landmarks[].type | string | true | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.matchedFaceDetails[].faceDetail.landmarks[].x | float | true | 0.362 | x 좌표 |
| data.matchedFaceDetails[].faceDetail.landmarks[].y | float | true | 0.362 | y 좌표 |
| data.matchedFaceDetails[].faceDetail.confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |
| data.matchedFaceDetails[].similarity | float | true | 98.156 | 0\~100 값을 가지는 유사도 |
| data.unmatchedFaceDetailCount | int | true | 1 | unmatched face 개수 |
| data.unmatchedFaceDetails[].faceDetail.bbox | object | true | - | 얼굴 detect된 bbox |
| data.unmatchedFaceDetails[].faceDetail.bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.unmatchedFaceDetails[].faceDetail.bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.unmatchedFaceDetails[].faceDetail.bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.unmatchedFaceDetails[].faceDetail.bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.unmatchedFaceDetails[].faceDetail.landmarks | array | true | - | 얼굴 특징 |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].type | string | true | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].x | float | true | 0.362 | x 좌표 |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].y | float | true | 0.362 | y 좌표 |
| data.unmatchedFaceDetails[].faceDetail.confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |
| data.unmatchedFaceDetails[].similarity | float | true | 98.156 | 0\~100 값을 가지는 유사도 |
| data.sourceFace.bbox | object | true | - | 얼굴 detect된 bbox |
| data.sourceFace.bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.sourceFace.bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.sourceFace.bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.sourceFace.bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.sourceFace.confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |

#### Error Codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| SourceImageImageTooLargeException | -45000 | source Image: exceed image size |
| SourceImageInvalidImageFormatException | -45000 | source image: not supported image format |
| SourceImageInvalidImageURLException | -45000 | source image: invalid image url |
| SourceImageImageTimeoutError | -45000 | source image: image download timeout |
| TargetImageImageTooLargeException | -45000 | target image: exceed image size |
| TargetImageInvalidImageFormatException | -45000 | target image: not supported image format |
| TargetImageInvalidImageURLException | -45000 | target image: invalid image url |
| TargetImageImageTimeoutError | -45000 | target image: image download timeout |

## Service API Data Type

* Service API 에서 사용하는 Data type에 대한 정의

### Image

* 사용자가 파라미터로 넘기는 이미지의 정보

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| url | string | false | "https://..." | 이미지 url |
| bytes | string | false | "/9j/4O1Q==..." | 이미지 bytes 코드. url 또는 bytes가 둘 중 하나만 있어야 한다. |

### Face

* 응답으로 나가는 얼굴의 정보

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| faceId | string | false | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | face Id. 등록된 경우에만 있다. |
| imageId | string | false | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | image Id. 등록된 경우에만 있다. |
| externalImageId | string | false | "a.jpg" | 사용자가 등록한 값. 등록된 경우에만 있다. |
| bbox | object | true | - | 얼굴 detect된 bbox |
| bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |

### FaceDetail

* 응답으로 나가는 얼굴의 정보

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| bbox | object | true | - | 얼굴 detect된 bbox |
| bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| landmarks | array | true | - | 얼굴 특징 |
| landmarks[].type | float | true | leftEye | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| landmarks[].y | float | true | 0.362 | y 좌표 |
| landmarks[].x | float | true | 0.362 | x 좌표 |
| confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |

### MatchedFaces

* target image중 source image에서 매칭된 얼굴

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| FaceDetail | data type | true | - | Face 정보 |
| similarity | float | true | 0.32165 | 유사도 |

### UnmatchedFaces

* target image중 source image에서 매칭 안된 얼굴

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| FaceDetail | data type | true | - | Face 정보 |
| similarity | float | true | 0.32165 | 유사도 |

### Group

* 사용자가 등록한 Group 정보

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| groupId | string | true | "nhn" | [a-z0-9-]{1,255} 사용자가 등록한 group id |
| modelVersion | string | true | "v1.0" | NHN Face Recognition version 정보 |

### GroupDetail

* 사용자가 등록한 Group 정보

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| groupId | string | true | "nhn" | [a-z0-9-]{1,255} 사용자가 등록한 group id |
| modelVersion | string | true | "v1.0" | NHN Face Recognition version 정보 |
| createTime | string | false | "2020-11-04T12:36:24" | Group을 생성한 시간. |
| faceCount | int | false | 365 | 그룹에 등록된 face Id 숫자. 그룹 상세에서만 볼 수 있다. |
