# [NFR][Service API] API 문서

* 얼굴인식 API를 사용하는 데 필요한 API르 설명합니다.

## 목차
[API 리스트](#API)
1. <a href="#API">API 리스트</a>
    1. <a href="#Create-Group">Create Group</a>
    2. <a href="#List-Groups">List Groups</a>
    3. <a href="#Get-Group-Detail">Get Group Detail</a>
    4. <a href="#Delete-Group">Delete Group</a>
    5. <a href="#Detect-Faces">Detect Faces</a>
    6. <a href="#Add-Faces">Add Faces</a>
    7. <a href="#Delete-Face">Delete Face</a>
    8. <a href="#List-Faces-in-a-Group">List Faces in a Group</a>
    9. <a href="#Search-Faces-by-Face-ID">Search Faces by Face ID</a>
    10. <a href="#Search-Faces-by-Image">Search Faces by Image</a>
    11. <a href="#Compare-Faces">Compare Faces</a>
2. <a href="#Service-API-Data-Type">Service API Data Type</a>
    1. <a href="#Image">Image</a>
    2. <a href="#Face">Face</a>
    3. <a href="#FaceDetail">FaceDetail</a>
    4. <a href="#MatchedFaces">MatchedFaces</a>
    5. <a href="#UnmatchedFaces">UnmatchedFaces</a>
    6. <a href="#Group">Group</a>
    7. <a href="#GroupDetail">GroupDetail</a>

## API

* 기본적으로 회사 가이드를 따르고, QPIT API 포멧 공통화 가이드를 참고합니다.
    * [(선별)ToastCloud/467 RestAPI Guide](dooray://1387695619080878080/tasks/1804779257775080328 "closed")
    * [(검색)QPIT대외사업/15 API 포멧 공통화](dooray://1387695619080878080/tasks/2519396097881722280 "working")
* path pattern: {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/
    * `domain`: [frn.search.nhn.com](http://frn.search.nhn.com)과 같이 외부에 노출된 domain
    * `api-version`: api version
    * `appkey`: 고객사에 발급된 key

### 공통 header

* `Content-Type: application/json`
* api 가 요청을 받으면, Http Response Code 는 항상 `200 OK`

### 기본 response body

| name | type | desc |
| --- | --- | --- |
| header.resultCode | int | 0: 정상<br>양수: 일부 오류가 있으나 동작 함<br>-40000: InvalidParam<br>-41000: Unauthorized appkey.<br>-45000: InvalidImage<br>-50000: Internal Server Error |
| header.resultMessage | string | "SUCCESS": 정상.<br>그 외: 오류 메시지 리턴 |
| header.isSuccessful | boolean | true: 정상, 동작 함.<br>false: 오류 |
| data.itemCount | int | item의 갯수 |
| data.items[] | array | 결과 값. 단일 결과도 배열 형태로 리턴 |

* example

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "data": {
        "itemCount": 1,
        "items": [{
            "groupId": "group-id",
            "faceSize": 5000,
            "createTime": "2020-10-29T14:34:12"
        }]
    }

}
```

### Create Group

* 그룹 생성

#### request

```
POST {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups
{
    "groupId": "my-group"
}
```

* params

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| groupId | string | true | "my-group" | 사용자가 등록한 group id<br>[a-z0-9-]{1,255} |

#### response

json example

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

* 공통 헤더 설명 생략

#### error codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| InvalidGroupID | -40000 | invalid group ID pattern |
| DuplicatedGroupIDError | -40000 | duplicated group ID |

### List Groups

* 그룹 리스트 조회

#### request

```
GET {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups?limit={limit}&next-token={next-token}
```

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| limit | int | true | 100 | 최대 크기<br>0 < limit <= 200 |
| next-token | string | false | "skljsdioew..." | Get Group List response에 존재하는 값. next-token 이후로 limit를 세어서 리턴 |

* `주의 사항`
    * 처음에는 next-token이 존재 할 수 없다.
    * token은 특정 시간이나 특정 조건에서 사라질 수 있다.
    * token 발행 시 limit는 고정된다.
* 시나리오 example)

1. 최초 query

```
GET {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups?limit={limit}
```

2. response 에 포함된 nextToken을 이용하여 query

```
GET {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups?next-token={next-token}&limit={limit}
```

* next-token이 존재하면 limit는 변경 될 수 없으며 token이 발행 될 때의 값으로 자동 세팅된다.

#### response

json example

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

* 공통 헤더 설명 생략

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| data.groupCount | int | true | 2 | group count |
| data.groups[].groupId | string | true | "group-id" | 사용자가 등록한 group id |
| data.groups[].modelVersion | string | true | "v1.0" | NHN Face Recognition version 정보 |
| data.nextToken | string | true | "dlkj-210jwoivndslko9d..." | paging에서 사용할 token. request에 next-token 파라미터를 전달하면 그 이후부터 센다. |

#### error codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| InvalidTokenError | -40000 | using wrong token |


### Get Group Detail

* 그룹 상세 정보 조회

#### request

```
GET {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}
```

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| group-id | string | true | "group-id" | 사용자가 등록한 group id<br>[a-z0-9-]{1,255} |

#### response

json example

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

* 공통 헤더 설명 생략

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| data.groupCount | int | true | 2 | group count |
| data.groups[].groupId | string | true | "group-id" | 사용자가 등록한 group id |
| data.groups[].modelVersion | string | true | "v1.0" | NHN Face Recognition version 정보 |
| data.groups[].createTime | string | true | "2020-11-04T12:36:24" | Group을 생성한 시간. |
| data.groups[].faceCount | int | false | 365 | 그룹에 등록된 face Id 숫자 |

#### error codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| NotFoundGroupError | -40000 | not found group ID |

### Delete Group

* 그룹 삭제

#### request

```
DELETE {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}
```

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| group-id | string | true | "group-id" | 등록된 group id<br>[a-z0-9-]{1,255} |

#### response

json example

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

* 공통 헤더 설명 생략

#### error codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| NotFoundGroupError | -40000 | not found group ID |

### Detect Faces

* 이미지로부터 얼굴 정보를 가지고 온다.
* detect 된 face가 큰 순서대로 최대 `100`개의 얼굴 정보를 리턴한다.

#### request

```
POST {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/detect
{
    "image": {
        "url":"https://..."
    }
}
```

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| image.url | string | false | "https://..." | 이미지의 url |
| image.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes |

* image.url, image.bytes 중 반드시 1개가 있어야 한다.

#### response

json example

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

* 공통 헤더 설명 생략

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| data.faceDetailCount | int | true | 1 | detected face 개수 |
| data.faceDetails[].bbox | object | true | - | 얼굴 detect된 bbox |
| data.faceDetails[].bbox.x0 | float | true | 0.123 | 얼굴 bbox의 x0 좌표 |
| data.faceDetails[].bbox.y0 | float | true | 0.123 | 얼굴 bbox의 y0 좌표 |
| data.faceDetails[].bbox.x1 | float | true | 0.123 | 얼굴 bbox의 x1 좌표 |
| data.faceDetails[].bbox.y1 | float | true | 0.123 | 얼굴 bbox의 y1 좌표 |
| data.faceDetails[].landmarks | array | true | - | 얼굴 특징 |
| data.faceDetails[].landmarks[].type | string | true | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.faceDetails[].landmarks[].y | float | true | 0.362 | y 좌표 |
| data.faceDetails[].landmarks[].x | float | true | 0.362 | x 좌표 |
| data.faceDetails[].confidence | float | true | 99.9123 | 얼굴 인식 신뢰도 |

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

### Add Faces

* 이미지를 통해 detect된 faces를 특정 그룹에 등록한다.

#### request

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

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| group id | url path | true | "my-group" | Create Group을 통해 등록된 group id<br>[a-z0-9-]{1,255} |
| image.url | string | false | "https://..." | 이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| image.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| externalImageId | string | false | "image01.jsp" | 사용자가 이미지 또는 Face ID들에 labeling을 위해 전달하는 값<br>[a-zA-Z0-9\_.-:]+<br>1 <limit <= 255 |
| limit | int | true | 3 | 전달된 이미지에서 detected faces 중 크기가 큰 순으로 정렬하여 최대 그룹에 등록할 face 개수<br>0< limit <= 100 |

#### response

json example

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

* 공통 헤더 설명 생략

| name | type | mandatory | example | desc |
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

### Delete Face

* 등록된 face 삭제

#### request

```
DELETE {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}/faces/{face-id}
```

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| group-id | url path | true | "group-id" | 등록된 group id<br>[a-z0-9-]{1,255} |
| face-id | url path | true | "group-id" | 등록된 face id |

#### response

json example

``` json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

* 공통 헤더 설명 생략

#### Error Codes

| name | type | desc |
| --- | --- | --- |
| InternalServerError | -50000 | internal server error |
| InvalidParam | -40000 | invalid param |
| UnauthorizedAppKey | -41000 | unauthorized appkey |
| NotFoundGroupError | -40000 | not found group ID |
| NotFoundFaceIDError | -40000 | not found face ID |

### List Faces in a Group

* 특정 그룹에 등록된 face list 조회

#### request

```
GET {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}/faces?limit={limit}&next-token={next-token}
```

| name | type | mandatory | example | desc |
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

#### response

json example

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

* 공통 헤더 설명 생략

| name | type | mandatory | example | desc |
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

### Search Faces by Face ID

* face id로 특정 그룹에서 검색

#### request

```
GET {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}/faces/{face-id}?limit={limit}&threshold ={threshold }
```

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| group-id | url path | true | "group-id" | 등록된 group id<br>[a-z0-9-]{1,255} |
| face-id | url path | true | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | 비교하려는 face id |
| limit | int | true | 10 | 찾으려는 최대 값.<br>0 < limit <= 4096 |
| threshold | int | true | 90 | 유사도<br>0 < threshold <= 100 |

#### response

json example

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

* 공통 헤더 설명 생략

| name | type | mandatory | example | desc |
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

### Search Faces by Image

* image로 특정 그룹에서 검색
* 전달된 이미지에서 detected faces 중 가장 큰 이미지를 비교대상으로 사용한다.

#### request

```
POST {domain}/nhn-face-reco/{api-version}/appkeys/{appkey}/groups/{group-id}/search?limit={limit}&threshold ={threshold }
{
    "image": {
        "url": "https://..."
    }
}
```

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| group id | url path | true | "my-group" | Create Group을 통해 등록된 group id<br>[a-z0-9-]{1,255} |
| image.url | string | false | "https://..." | 이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| image.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| limit | int | true | 10 | 찾으려는 최대 값.<br>0 < limit <= 4096 |
| threshold | int | true | 90 | 유사도<br>0 < threshold <= 100 |

#### response

json example

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

* 공통 헤더 설명 생략

| name | type | mandatory | example | desc |
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

### Compare Faces

* 두 이미지를 비교한다.
* Source Image에 2개 이상의 face가 detect된다면 가장 큰 face를 사용한다.

#### request

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

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| sourceImage | object | true | - | 주어진 이미지에서 detected faces 중 가장 큰 face를 source로 사용하여 targets과 비교한다. |
| sourceImage.url | string | false | "https://..." | <br>이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| sourceImage.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| targetImage | object | true | - | source Image와 비교할 대상 |
| targetImage.url | string | false | "https://..." | <br>이미지의 url<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| targetImage.bytes | blob | false | "/0j3Ohdk==..." | 이미지의 bytes<br>image.url, image.bytes 중 반드시 1개가 있어야 한다. |
| threshold | int | true | 90 | 유사도<br>0 < threshold <= 100 |

#### response

json example

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

* 공통 헤더 설명 생략

| name | type | mandatory | example | desc |
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

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| url | string | false | "https://..." | 이미지 url |
| bytes | string | false | "/9j/4O1Q==..." | 이미지 bytes 코드. url 또는 bytes가 둘 중 하나만 있어야 한다. |

### Face

* 응답으로 나가는 얼굴의 정보

| name | type | mandatory | example | desc |
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

| name | type | mandatory | example | desc |
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

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| FaceDetail | data type | true | - | Face 정보 |
| similarity | float | true | 0.32165 | 유사도 |

### UnmatchedFaces

* target image중 source image에서 매칭 안된 얼굴

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| FaceDetail | data type | true | - | Face 정보 |
| similarity | float | true | 0.32165 | 유사도 |

### Group

* 사용자가 등록한 Group 정보

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| groupId | string | true | "nhn" | [a-z0-9-]{1,255} 사용자가 등록한 group id |
| modelVersion | string | true | "v1.0" | NHN Face Recognition version 정보 |

### GroupDetail

* 사용자가 등록한 Group 정보

| name | type | mandatory | example | desc |
| --- | --- | --- | --- | --- |
| groupId | string | true | "nhn" | [a-z0-9-]{1,255} 사용자가 등록한 group id |
| modelVersion | string | true | "v1.0" | NHN Face Recognition version 정보 |
| createTime | string | false | "2020-11-04T12:36:24" | Group을 생성한 시간. |
| faceCount | int | false | 365 | 그룹에 등록된 face Id 숫자. 그룹 상세에서만 볼 수 있다. |
