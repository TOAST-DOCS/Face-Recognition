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
3. [그룹 상세정보](#그룹-상세정보)
4. [그룹 삭제](#그룹-삭제)
5. [얼굴 감지](#얼굴-감지)
6. [얼굴 추가](#얼굴-추가)
7. [얼굴 삭제](#얼굴-삭제)
8. [그룹 내 얼굴 목록](#그룹-내-얼굴-목록)
9. [얼굴 아이디로 검색](#얼굴-아이디로-검색)
10. [얼굴 이미지로 검색](#얼굴-이미지로-검색)
11. [얼굴 이미지 비교](#얼굴-이미지-비교)


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
| groupId | string | O | "my-group" | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |



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

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -40000 | InvalidGroupID | 그룹 아이디 오류 |
| -40000 | DuplicatedGroupID | 중복된 그룹 아이디 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 appKey |
| -50000 | InternalServerError | 서버 에러 |

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

2. "그룹 목록 응답 본문 data"에 포함된 nextToken을 이용하여 요청

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
| data.groupCount | int | O | 2 | 그룹 수 |
| data.groups[].groupId | string | O | "group-id" | 사용자가 등록한 그룹 아이디 |
| data.groups[].modelVersion | string | O | "v1.0" | 얼굴 감지 모델 버젼 정보 |
| data.nextToken | string | O | "dlkj-210jwoivndslko9d..." | paging에서 사용할 token. request에 next-token 파라미터를 전달하면 그 이후부터 센다. |

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | 서버 에러 |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 appKey |
| -40000 | InvalidTokenError | 잘못된 토큰 사용 |

### 그룹 상세정보

* 그룹 상세 정보 조회
* 그룹에 있는 얼굴 수와 그룹에서 얼굴 감지를 위해 사용하는 모델 버젼 같은 정보를 가지고 옵니다. 

#### 요청
[URI]

| 메서드 | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |

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
| data.groupCount | int | true | 2 | 그룹 수 |
| data.groups[].groupId | string | true | "group-id" | 사용자가 등록한 그룹 아이디 |
| data.groups[].modelVersion | string | true | "v1.0" | 얼굴 감지 모델 버젼 정보 |
| data.groups[].createTime | string | true | "2020-11-04T12:36:24" | 그룹을 생성한 시간. |
| data.groups[].faceCount | int | false | 365 | 그룹에 등록된 얼굴 수 |

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | 서버 에러 |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 appKey |
| -40000 | NotFoundGroupError | 그룹 아이디를 찾을 수 없습니다 |

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
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |

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

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | 서버 에러 |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 appKey |
| -40000 | NotFoundGroupError | 그룹 아이디를 찾을 수 없습니다 |

### 얼굴 감지

* 요청으로 받은 이미지로내의 얼굴을 감지합니다.
* `얼굴 감지`는 이미지에서 얼굴이 큰 순서대로 최대 `100`개의 얼굴 정보를 감지합니다.
* 지원하는 이미지 포맷은 `PNG`, `JPEG`입니다. 

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/detect |

[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |

[Request Body]

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| image.url | string | false | "https://..." | 이미지의 url |
| image.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes |

* image.url, image.bytes 중 반드시 1개가 있어야 한다.

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
| data.faceDetailCount | int | O | 1 | 감지된 얼굴 수 |
| data.faceDetails[].bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.faceDetails[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.faceDetails[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.faceDetails[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.faceDetails[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.faceDetails[].landmarks | array | O | - | 얼굴 특징 |
| data.faceDetails[].landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.faceDetails[].landmarks[].y | float | O | 0.362 | y 좌표 |
| data.faceDetails[].landmarks[].x | float | O | 0.362 | x 좌표 |
| data.faceDetails[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |

#### Error Codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | 서버 에러 |
| InvalidParam | -40000 | 파라미터에 오류가 있음 |
| UnauthorizedAppKey | -41000 | 승인되지 않은 appKey |
| ImageTooLargeException | -45000 | 이미지 크기 초과 |
| InvalidImageFormatException | -45000 | 지원하지 않는 이미지 포멧 |
| InvalidImageURLException | -45000 | 잘못된 이미지 URL |
| ImageTimeoutError | -45000 | 이미지 다운로드 시간 초과 |

### 얼굴 추가

* 요청으로 전달된 이미지에서 감지된 얼굴들을 특정한 그룹에 등록한다.
* NHN 얼굴인식은 요청으로 전달된 이미지도 인식된 얼굴도 저장하지 않습니다. 대신 요청으로 전달된 이미지에서 얼굴의 box를 감지하고 감지된 얼굴 box에서 얼굴 특징을 벡터로 추출합니다. 추출된 벡터 데이터는 암호화되어 데이터베이스에 저장됩니다.
* 추가된 얼굴 벡터는 [얼굴 아이디로 검색](#얼굴-아이디로-검색), [얼굴 이미지로 검색](#얼굴-이미지로-검색) API를 통해 얼굴 검색할 때 특징 벡터로 사용됩니다.

#### 요청

[URI]
 
| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |
 


[Request Body]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| image.url | string |  | "https://..." | 이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| image.bytes | blob |  | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| externalImageId | string |  | "image01.jsp" | 사용자가 이미지 또는 얼굴 아이디들에 라벨링을 위해 전달하는 값<br>[a-zA-Z0-9\_.-:]+<br>1 <limit <= 255 |
| limit | int | O | 3 | 전달된 이미지에서 인식된 얼굴들 중 크기가 큰 순으로 정렬하여 최대 그룹에 등록할 얼굴 수<br>0< limit <= 100 |

* image.url, image.bytes 중 반드시 1개가 있어야 한다.

 
[요청 예]
 
```shell script
curl -X POST '/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}' \
 -H 'Authorization: {secretKey}' \
 -H 'Content-Type: application/json;charset=UTF-8' \
 -d '{
    "image": {
        "url": "https://..."
    },
    "externalImageId": "image01.jpg",
    "limit": 3
}'
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

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | 얼굴 감지 모델 정보 |
| data.addedFaceCount | int | O | 1 | 추가된 얼굴 수 |
| data.addedFaces[].bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.addedFaces[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.addedFaces[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.addedFaces[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.addedFaces[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.addedFaces[].faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | 얼굴 아이디 |
| data.addedFaces[].imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 아이디. 한 이미지 아이디에 여러 얼굴 아이디가 존재 할 수 있다. |
| data.addedFaces[].externalImageId | string |  | "image01.jpg" | 요청에서 전달된 값 |
| data.addedFaces[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.addedFaceDetails[].bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.addedFaceDetails[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.addedFaceDetails[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.addedFaceDetails[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.addedFaceDetails[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.addedFaceDetails[].landmarks | array | O | - | 얼굴 특징 |
| data.addedFaceDetails[].landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.addedFaceDetails[].landmarks[].y | float | O | 0.362 | y 좌표 |
| data.addedFaceDetails[].landmarks[].x | float | O | 0.362 | x 좌표 |
| data.addedFaceDetails[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.notAddedFaceCount | int | O | 1 | 추가안된 얼굴 수 |
| data.notAddedFaces[].bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.notAddedFaces[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.notAddedFaces[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.notAddedFaces[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.notAddedFaces[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.notAddedFaces[].landmarks | array | O | - | 얼굴 특징 |
| data.notAddedFaces[].landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.notAddedFaces[].landmarks[].x | float | O | 0.362 | x 좌표 |
| data.notAddedFaces[].landmarks[].y | float | O | 0.362 | y 좌표 |
| data.notAddedFaces[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | 서버 에러 |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 appKey |
| -40000 | NotFoundGroupError | 그룹 아이디를 찾을 수 없습니다 |
| -45000 | ImageTooLargeException | 이미지 크기 초과 |
| -45000 | InvalidImageFormatException | 지원하지 않는 이미지 포멧 |
| -45000 | InvalidImageURLException | 잘못된 이미지 URL |
| -45000 | ImageTimeoutError | 이미지 다운로드 시간 초과 |

### 얼굴 삭제

* 그룹에서 등록된 얼굴을 삭제합니다. 

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| DELETE | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id} |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |
| face-id | 등록된 얼굴 아이디 |
 
[요청 예]
 
```shell script
curl -X DELETE '/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}' \
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

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | 서버 에러 |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 appKey |
| -40000 | NotFoundGroupError | 그룹 아이디를 찾을 수 없습니다 |
| -40000 | NotFoundFaceIDError | 얼굴 아이디를 찾을 수 없습니다 |

### 그룹 내 얼굴 목록

* 특정 그룹에 등록된 얼굴 목록 조회

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |
 
[URL Parameter]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| limit | int  | O  | 100  | 최대 크기<br>0 < limit <= 200 |
| next-token | string  |    | "skljsdioew..."  | "그룹 목록 응답 본문 data"에서 반환된 값. next-token 이후로 limit를 세어서 리턴 |
 

* `주의 사항`
    * 처음에는 next-token이 존재 할 수 없다.
    * token은 특정 시간이나 특정 조건에서 사라질 수 있다.
    * token 발행 시 limit는 고정된다.
* 시나리오 example)

1. 최초 query

 
[요청 예]
 
```shell script
curl -X GET '/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces?limit={limit}' \
 -H 'Authorization: {secretKey}' \
 -H 'Content-Type: application/json;charset=UTF-8' 
```


2. "그룹 목록 응답 본문 data"에 포함된 nextToken을 이용하여 요청
 
[요청 예]
 
```shell script
curl -X GET '/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces?limit={limit}&next-token={next-token}' \
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

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | 얼굴 감지 모델 정보 |
| data.faceCount | int | O | 2 | 감지된 얼굴 수 |
| data.faces[].bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.faces[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.faces[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.faces[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.faces[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.faces[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.faces[].faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | 얼굴 아이디 |
| data.faces[].imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 아이디. 한 이미지 아이디에 여러 얼굴 아이디가 존재 할 수 있다. |
| data.faces[].externalImageId | string |  | "image01.jpg" | 사용자가 이미지에 등록한 값 |
| data.nextToken | string | O | "dlkj-210jwoivndslko9d..." | paging에서 사용할 token. request에 next-token 파라미터를 전달하면 그 이후부터 센다. |

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | 서버 에러 |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 appKey |
| -40000 | NotFoundGroupError | 그룹 아이디를 찾을 수 없습니다 |
| -40000 | InvalidTokenError | 잘못된 토큰 사용 |

### 얼굴 아이디로 검색

* 얼굴 아이디로 특정 그룹에서 검색

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id} |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |
| face-id | 비교하려는 얼굴 아이디 |
 
[URL Parameter]
 
| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| limit | int  | O  | 100  | 찾으려는 최대 값.<br>0 < limit <= 4096 |
| threshold | int  | O  | 90  | 유사도<br>0 < threshold <= 100 |
 
[요청 예]
 
```shell script
curl -X GET '/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}?limit={limit}&threshold' \
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

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.matchFaceCount | int | O | 2 | 감지된 얼굴 수 |
| data.matchFaces[].face.bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.matchFaces[].face.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.matchFaces[].face.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.matchFaces[].face.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.matchFaces[].face.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.matchFaces[].face.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.matchFaces[].face.faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | 얼굴 아이디 |
| data.matchFaces[].face.imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 아이디. 한 이미지 아이디에 여러 얼굴 아이디가 존재 할 수 있다. |
| data.matchFaces[].face.externalImageId | string |  | "image01.jpg" | 사용자가 이미지에 등록한 값 |
| data.matchFaces[].similarity | float | O | 98.156 | 0\~100 값을 가지는 유사도 |

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | 서버 에러 |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 appKey |
| -40000 | NotFoundGroupError | 그룹 아이디를 찾을 수 없습니다 |
| -40000 | NotFoundFaceIDError | 얼굴 아이디를 찾을 수 없습니다 |

### 얼굴 이미지로 검색

* image로 특정 그룹에서 검색
* 전달된 이미지에서 detected faces 중 가장 큰 이미지를 비교대상으로 사용한다.

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/search |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |
| group-id | 사용자가 등록한 group id<br>[a-z0-9-]{1,255} |
 
[URL Parameter]
 
| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| limit | int  | O  | 100  | 찾으려는 최대 값.<br>0 < limit <= 4096 |
| threshold | int  | O  | 90  | 유사도<br>0 < threshold <= 100 |

[Request Body]
 
| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| image.url | string |  | "https://..." | 이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| image.bytes | blob |  | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |

* image.url, image.bytes 중 반드시 1개가 있어야 한다.
 
[요청 예]
 
```shell script
curl -X POST '/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/search?limit={limit}&threshold={threshold}' \
 -H 'Authorization: {secretKey}' \
 -H 'Content-Type: application/json;charset=UTF-8' \
 -d '{
    "image": {
        "url": "https://..."
    }
}'
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

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.matchFaceCount | int | O | 2 | 감지된 얼굴 수 |
| data.matchFaces[].face.bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.matchFaces[].face.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.matchFaces[].face.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.matchFaces[].face.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.matchFaces[].face.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.matchFaces[].face.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.matchFaces[].face.faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | 얼굴 아이디 |
| data.matchFaces[].face.imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 아이디. 한 이미지 아이디에 여러 얼굴 아이디가 존재 할 수 있다. |
| data.matchFaces[].face.externalImageId | string |  | "image01.jpg" | 사용자가 이미지에 등록한 값 |
| data.matchFaces[].similarity | float | O | 98.156 | 0\~100 값을 가지는 유사도 |
| data.sourceFace.bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.sourceFace.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.sourceFace.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.sourceFace.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.sourceFace.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.sourceFace.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | 서버 에러 |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 appKey |
| -40000 | NotFoundGroupError | 그룹 아이디를 찾을 수 없습니다 |
| -45000 | ImageTooLargeException | 이미지 크기 초과 |
| -45000 | InvalidImageFormatException | 지원하지 않는 이미지 포멧 |
| -45000 | InvalidImageURLException | 잘못된 이미지 URL |
| -45000 | ImageTimeoutError | 이미지 다운로드 시간 초과 |

### 얼굴 이미지 비교

* 두 이미지를 비교한다.
* 소스 이미지에 2개 이상의 얼굴이 감지된다면 가장 큰 얼굴을 사용한다.

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/compare |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 고유의 appKey |
 
[Request Body]
 
| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| sourceImage | object | O | - | 주어진 이미지에서 감지된 얼굴들 중 가장 큰 얼굴을 소스로 사용하여 타깃들과 비교한다. |
| sourceImage.url | string |  | "https://..." | <br>이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| sourceImage.bytes | blob |  | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| targetImage | object | O | - | 소스 이미지와 비교할 대상 |
| targetImage.url | string |  | "https://..." | <br>이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| targetImage.bytes | blob |  | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| threshold | int | O | 90 | 유사도<br>0 < threshold <= 100 |
 
[요청 예]
 
```shell script
curl -X POST '/nhn-face-reco/v1.0/appkeys/{appKey}/compare' \
 -H 'Authorization: {secretKey}' \
 -H 'Content-Type: application/json;charset=UTF-8' \
 -d '{
    "sourceImage": {
        "url": "https://..."
    },
    "targetImage": {
        "url": "https://..."
    },
    "threshold ": 80
}'
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

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | 얼굴 감지 모델 정보 |
| data.matchedFaceDetailCount | int | O | 1 | 매칭된 얼굴 수 |
| data.matchedFaceDetails[].faceDetail.bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.matchedFaceDetails[].faceDetail.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.matchedFaceDetails[].faceDetail.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.matchedFaceDetails[].faceDetail.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.matchedFaceDetails[].faceDetail.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.matchedFaceDetails[].faceDetail.landmarks | array | O | - | 얼굴 특징 |
| data.matchedFaceDetails[].faceDetail.landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.matchedFaceDetails[].faceDetail.landmarks[].x | float | O | 0.362 | x 좌표 |
| data.matchedFaceDetails[].faceDetail.landmarks[].y | float | O | 0.362 | y 좌표 |
| data.matchedFaceDetails[].faceDetail.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.matchedFaceDetails[].similarity | float | O | 98.156 | 0\~100 값을 가지는 유사도 |
| data.unmatchedFaceDetailCount | int | O | 1 | 매칭되지 않은 얼굴 수 |
| data.unmatchedFaceDetails[].faceDetail.bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.unmatchedFaceDetails[].faceDetail.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.unmatchedFaceDetails[].faceDetail.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.unmatchedFaceDetails[].faceDetail.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.unmatchedFaceDetails[].faceDetail.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.unmatchedFaceDetails[].faceDetail.landmarks | array | O | - | 얼굴 특징 |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].x | float | O | 0.362 | x 좌표 |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].y | float | O | 0.362 | y 좌표 |
| data.unmatchedFaceDetails[].faceDetail.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.unmatchedFaceDetails[].similarity | float | O | 98.156 | 0\~100 값을 가지는 유사도 |
| data.sourceFace.bbox | object | O | - | 이미지 내에서 감지된 얼굴의 box 정보 |
| data.sourceFace.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x0 좌표 |
| data.sourceFace.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y0 좌표 |
| data.sourceFace.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 x1 좌표 |
| data.sourceFace.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지된 얼굴의 box의 y1 좌표 |
| data.sourceFace.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -50000 | InternalServerError | 서버 에러 |
| -40000 | InvalidParam | 파라미터에 오류가 있음 |
| -41000 | UnauthorizedAppKey | 승인되지 않은 appKey |
| -45000 | ImageImageTooLargeException:{Source/Target} | {Source/Target} Image: 이미지 크기 초과 |
| -45000 | ImageInvalidImageFormatException:{Source/Target} | {Source/Target} image: 지원하지 않는 이미지 포멧 |
| -45000 | ImageInvalidImageURLException:{Source/Target} | {Source/Target} image: 잘못된 이미지 URL |
| -45000 | ImageImageTimeoutError:{Source/Target} | {Source/Target} image: 이미지 다운로드 시간 초과 |
