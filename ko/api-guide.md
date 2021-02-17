# AI Service > Face Recognition > 얼굴인식 API 가이드

* 얼굴인식 API를 사용하는 데 필요한 API를 설명합니다.

## API 공통 정보
### 사전 준비
- API 사용을 위해서는 앱 키와 보안 키가 필요합니다.
- 앱 키와 보안 키는 콘솔 상단 "URL & Appkey" 메뉴에서 확인이 가능합니다.

### 요청 공통 정보
- API를 사용하기 위해서는 보안 키 인증 처리가 필요합니다.
- 모든 API 요청 헤더에 'Authorization' 보안 키를 넣어서 요청해야 합니다.

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

[입력 이미지 가이드]

* 입력 이미지는 너비와 높이 모두 최소 80px 이상이어야 합니다.
    * 얼굴 크기가 최소 60x60px 이상이어야 얼굴 인식이 가능합니다.
    * 이미지 크기가 커질수록 최소 얼굴 크기도 커져야 더 정확하게 인식이 가능합니다.
    * 이미지에서 얼굴이 차지하는 비중이 클수록 더 정확하게 인식이 가능합니다.
* 입력 이미지의 너비 혹은 높이가 2048px을 초과하는 경우 원본 이미지 비율에 맞춰서 너비나 높이를 최대 2048px로 변환 후 사용합니다.
* 이미지 최대 크기: 최대 5MB
* 지원 이미지 포맷: `PNG`, `JPEG`

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

[실패 응답 본문 예]

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
6. [얼굴 등록](#얼굴-등록)
7. [얼굴 삭제](#얼굴-삭제)
8. [그룹 내 얼굴 목록](#그룹-내-얼굴-목록)
9. [페이스 아이디로 얼굴 검색](#페이스-아이디로-얼굴-검색)
10. [이미지로 얼굴 검색](#이미지로-얼굴-검색)
11. [얼굴 비교](#얼굴-비교)


### 그룹 생성

- 그룹을 생성하는 API입니다. 생성된 그룹에 "[얼굴 등록](#얼굴-등록)"을 이용하여 얼굴을 등록할 수 있습니다.

#### 요청
[URI]

| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |

[Request Body]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| groupId | string | O | "my-group" | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |


<details>
<summary>요청 예</summary>

```sh
curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8'  -d '{
    "groupId": "my-group"
}'
```

</details>

#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능


<details>
<summary>응답 본문 예</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-40010| InvalidGroupID | 그룹 아이디 오류 |
|-40020| DuplicatedGroupID | 중복된 그룹 아이디 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-50000| InternalServerError | 서버 에러 |

### 그룹 목록

* 그룹 목록을 조회하는 API입니다.

#### 요청

[URI]
| 메서드 | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups |


[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |

[URL Parameter]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | 최대 크기<br>0 < limit <= 200 |
| next-token | string  |    | "skljsdioew..."  | "그룹 목록 응답 본문 data"에서 반환한 값<br/> 결과가 잘린 경우 next-token을 이용하여 이후 결과를 가지고 올 수 있음 |


* `주의 사항`
    * 처음에는 next-token이 존재 할 수 없습니다.
    * token은 특정 시간이나 특정 조건에서 사라질 수 있습니다.
    * token 발행 시 limit은 고정됩니다.
* 시나리오 example)

1. 최초 query


<details>
<summary>요청 예</summary>

```shell script
curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups?limit={limit}'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

2. "그룹 목록 응답 본문 data"에 포함된 next-token을 이용하여 요청


<details>
<summary>요청 예</summary>

```shell script
curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups?limit={limit}&next-token={next-token}'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

* next-token이 존재하면 limit은 변경 될 수 없으며 token이 발행 될 때의 값으로 자동 세팅됨

#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.groupCount | int | O | 2 | 그룹 수 |
| data.groups[].groupId | string | O | "group-id" | 사용자가 등록한 그룹 아이디 |
| data.groups[].modelVersion | string | O | "v1.0" | 얼굴 감지 모델 버전 정보 |
| data.nextToken | string | O | "dlkj-210jwoivndslko9d..." | paging에서 사용할 token<br>결과가 잘린 경우 next-token을 이용하여 이후 결과를 가지고 올 수 있음 |

<details>
<summary>응답 본문 예</summary>

```json
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

</details>

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-40040| InvalidTokenError | 잘못된 토큰 사용 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-50000| InternalServerError | 서버 에러 |

### 그룹 상세정보

* 그룹 아이디, 모델 버전, 그룹에 등록한 얼굴 수 등 특정 그룹의 상세정보를 조회하는 API입니다.

#### 요청
[URI]

| 메서드 | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |

<details>
<summary>요청 예</summary>

```
curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.groupCount | int | O | 1 | 그룹 수 |
| data.groups[].groupId | string | O | "group-id" | 사용자가 등록한 그룹 아이디 |
| data.groups[].modelVersion | string | O | "v1.0" | 얼굴 감지 모델 버전 정보 |
| data.groups[].createTime | string | O | "2020-11-04T12:36:24" | 그룹을 생성한 시간 |
| data.groups[].faceCount | int |   | 365 | 그룹에 등록한 얼굴 수 |

<details>
<summary>응답 본문 예</summary>

```json
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

</details>

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-40030| NotFoundGroupError | 그룹 아이디를 찾을 수 없음 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-50000| InternalServerError | 서버 에러 |

### 그룹 삭제

* 특정 그룹과 그 그룹에 속한 얼굴 정보를 영구히 삭제하는 API입니다.

#### 요청

[URI]

| 메서드 | URI |
| --- | --- |
| DELETE | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |


<details>
<summary>요청 예</summary>

```shell script
curl -X DELETE '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>



#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

<details>
<summary>응답 본문 예</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-40030| NotFoundGroupError | 그룹 아이디를 찾을 수 없음 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-50000| InternalServerError | 서버 에러 |

### 얼굴 감지

* 입력 이미지에서 얼굴을 감지하는 API입니다.
* 감지한 얼굴에서 얼굴, 눈, 코, 입 등의 위치 정보와 신뢰도 값을 반환합니다.
* 입력 이미지에서 얼굴이 큰 순서대로 최대 `20`개의 얼굴을 감지합니다.
* 입력 이미지는 base64로 인코딩된 이미지 바이트로 전달하거나 이미지 URL로 전달할 수 있습니다.
* 입력 이미지에 대한 세부사항은 "[입력 이미지 가이드](#입력-이미지-가이드)"를 참고하시기 바랍니다.

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/detect |

[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |

[Request Body]

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| image.url | string |  | "https://..." | 이미지의 URL<br>image.url, image.bytes 중 반드시 1개만 있어야 함 |
| image.bytes | blob |  | "/0j3Ohdk==..." | base64로 인코딩된 이미지 바이트<br>image.url, image.bytes 중 반드시 1개만 있어야 함 |

* image.url, image.bytes 중 반드시 1개만 있어야 합니다.


<details>
<summary>요청 예</summary>
 
```shell script
curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/detect'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8'  -d '{
    "image": {
        "url":"https://..."
    }
}'
```

</details>

#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.faceDetailCount | int | O | 1 | 감지한 얼굴 수 |
| data.faceDetails[].bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.faceDetails[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.faceDetails[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.faceDetails[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.faceDetails[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.faceDetails[].landmarks | array | O | - | 얼굴 특징 |
| data.faceDetails[].landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.faceDetails[].landmarks[].y | float | O | 0.362 | 얼굴 특징의 y 좌표 |
| data.faceDetails[].landmarks[].x | float | O | 0.362 | 얼굴 특징의 x 좌표 |
| data.faceDetails[].orientation | object | O | 0.362 | 얼굴 방향의 좌표 |
| data.faceDetails[].orientation.x | float | O | 15.303436 | 얼굴 방향의 x 좌표 |
| data.faceDetails[].orientation.y | float | O | -9.222179 | 얼굴 방향의 y 좌표 |
| data.faceDetails[].orientation.z | float | O | -7.97249 | 얼굴 방향의 z 좌표 |
| data.faceDetails[].confidence | float | O | 99.9123 | 얼굴인식 신뢰도 |


<details>
<summary>응답 본문 예</summary>

```json
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
            "orientation": {
                "x": 15.303436,
                "y": -9.222179,
                "z": -7.97249
            },
            "confidence": 99.8945155187
        }]
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-45020| ImageTooLargeException | 이미지 크기 초과 |
|-45040| InvalidImageFormatException | 지원하지 않는 이미지 포맷 |
|-45050| InvalidImageURLException | 잘못된 이미지 URL |
|-45060| ImageTimeoutError | 이미지 다운로드 시간 초과 |
|-50000| InternalServerError | 서버 에러 |

### 얼굴 등록

* 입력 이미지에서 감지한 얼굴을 특정 그룹에 등록하는 API입니다.
* 입력 이미지에서 얼굴의 box를 감지하고 감지한 얼굴 box에서 얼굴 특징을 벡터로 추출합니다. 이때, 입력 이미지와 입력 이미지에서 감지한 얼굴 이미지 그 어느 것도 저장하지 않습니다.
* 추출한 벡터 데이터는 암호화하여 데이터베이스에 저장합니다.
* 저장한 벡터 데이터는 [페이스 아이디로 얼굴 검색](#페이스-아이디로-얼굴-검색), [이미지로 얼굴 검색](#이미지로-얼굴-검색) API에 특징 벡터로 사용합니다.
* 입력 이미지는 base64로 인코딩된 이미지 바이트로 전달하거나 이미지 URL로 전달할 수 있습니다.
* 입력 이미지에 대한 세부사항은[입력 이미지 가이드](#입력-이미지-가이드)를 참고하시기 바랍니다.
* `imageId`는 입력 이미지에 부여되는 값이며 `externalImageId`는 사용자가 직접 부여할 수 있는 값입니다. 사용자는 `imageId`와 `externalImageId`를 통해 사용자 단에서 이미지 또는 페이스 아이디에 라벨링하고 인덱스처럼 자체적으로 활용할 수 있습니다.
* `imageId`와 `externalImageId`는 [그룹 내 얼굴 목록](#그룹-내-얼굴-목록)과 [페이스 아이디로 얼굴 검색](#페이스-아이디로-얼굴-검색), [이미지로 얼굴 검색](#이미지로-얼굴-검색) API의 응답에서 반환됩니다. 
* 단일 그룹에 등록할 수 있는 최대 얼굴 개수는 10만 개입니다.
 
#### 요청

[URI]
 
| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |
 

[Request Body]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| image.url | string |  | "https://..." | 이미지의 URL<br>image.url, image.bytes 중 반드시 1개만 있어야 함 |
| image.bytes | blob |  | "/0j3Ohdk==..." | base64로 인코딩된 이미지 바이트<br>image.url, image.bytes 중 반드시 1개만 있어야 함 |
| externalImageId | string |  | "image01.jsp" | 사용자가 이미지 또는 페이스 아이디에 라벨링을 하기 위해 전달하는 값<br>[a-zA-Z0-9\_.-:]+<br>1 <limit <= 255 |
| limit | int | O | 3 | 입력 이미지에서 인식한 얼굴 중 크기가 큰 순으로 정렬하여 그룹에 등록할 최대 얼굴 수<br>0< limit <= 20 |

* image.url, image.bytes 중 반드시 1개만 있어야 합니다.

 

<details>
<summary>요청 예</summary>
 
```shell script
curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8'  -d '{
    "image": {
        "url": "https://..."
    },
    "externalImageId": "image01.jpg",
    "limit": 3
}'
```

</details>


#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | 얼굴 감지 모델 정보 |
| data.addedFaceCount | int | O | 1 | 등록한 얼굴 수 |
| data.addedFaces[].bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.addedFaces[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.addedFaces[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.addedFaces[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.addedFaces[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.addedFaces[].faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | 페이스 아이디 |
| data.addedFaces[].imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 아이디<br>하나의 이미지 아이디에 여러 페이스 아이디가 존재 할 수 있음 |
| data.addedFaces[].externalImageId | string |  | "image01.jpg" | 요청에서 전달된 값 |
| data.addedFaces[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.addedFaceDetails[].bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.addedFaceDetails[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.addedFaceDetails[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.addedFaceDetails[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.addedFaceDetails[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.addedFaceDetails[].landmarks | array | O | - | 얼굴 특징 |
| data.addedFaceDetails[].landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.addedFaceDetails[].landmarks[].y | float | O | 0.362 | 얼굴 특징의 y 좌표 |
| data.addedFaceDetails[].landmarks[].x | float | O | 0.362 | 얼굴 특징의 x 좌표 |
| data.addedFaceDetails[].orientation | object | O | 0.362 | 얼굴 방향의 좌표 |
| data.addedFaceDetails[].orientation.x | float | O | 15.303436 | 얼굴 방향의 x 좌표 |
| data.addedFaceDetails[].orientation.y | float | O | -9.222179 | 얼굴 방향의 y 좌표 |
| data.addedFaceDetails[].orientation.z | float | O | -7.97249 | 얼굴 방향의 z 좌표 |
| data.addedFaceDetails[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.notAddedFaceCount | int | O | 1 | 등록하지 않은 얼굴 수 |
| data.notAddedFaces[].bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.notAddedFaces[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.notAddedFaces[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.notAddedFaces[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.notAddedFaces[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.notAddedFaces[].landmarks | array | O | - | 얼굴 특징 |
| data.notAddedFaces[].landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.notAddedFaces[].landmarks[].x | float | O | 0.362 | 얼굴 특징의 x 좌표 |
| data.notAddedFaces[].landmarks[].y | float | O | 0.362 | 얼굴 특징의 y 좌표 |
| data.notAddedFaces[].orientation | object | O | 0.362 | 얼굴 방향의 좌표 |
| data.notAddedFaces[].orientation.x | float | O | 15.303436 | 얼굴 방향의 x 좌표 |
| data.notAddedFaces[].orientation.y | float | O | -9.222179 | 얼굴 방향의 y 좌표 |
| data.notAddedFaces[].orientation.z | float | O | -7.97249 | 얼굴 방향의 z 좌표 |
| data.notAddedFaces[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |


<details>
<summary>응답 본문 예</summary>

```json
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
            "orientation": {
                "x": 15.303436,
                "y": -9.222179,
                "z": -7.97249
            },
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
            "orientation": {
                "x": 15.303436,
                "y": -9.222179,
                "z": -7.97249
            },
            "confidence": 99.8945155187

        }]

    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-40030| NotFoundGroupError | 그룹 아이디를 찾을 수 없음 |
|-40070| ServiceQuotaExceededException | 단일 그룹에 등록 가능한 최대 얼굴 개수 초과 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-45020| ImageTooLargeException | 이미지 크기 초과 |
|-45040| InvalidImageFormatException | 지원하지 않는 이미지 포맷 |
|-45050| InvalidImageURLException | 잘못된 이미지 URL |
|-45060| ImageTimeoutError | 이미지 다운로드 시간 초과 |
|-50000| InternalServerError | 서버 에러 |

### 얼굴 삭제

* 그룹에 등록한 특정 얼굴을 삭제하는 API입니다.

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| DELETE | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id} |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |
| face-id | 등록된 페이스 아이디 |
 

<details>
<summary>요청 예</summary>

```shell script
curl -X DELETE '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>

#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능


<details>
<summary>응답 본문 예</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-40030| NotFoundGroupError | 그룹 아이디를 찾을 수 없음 |
|-40050| NotFoundFaceIDError | 페이스 아이디를 찾을 수 없음 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-50000| InternalServerError | 서버 에러 |

### 그룹 내 얼굴 목록

* 특정 그룹에 등록한 얼굴 정보 목록을 조회하는 API입니다.
* 최근에 등록한 순으로 얼굴 정보 배열을 반환합니다.

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |
 
[URL Parameter]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| limit | int  | O  | 100  | 최대 크기<br>0 < limit <= 200 |
| next-token | string  |    | "skljsdioew..."  | "그룹 목록 응답 본문 data"에서 반환한 값<br/>결과가 잘린 경우 next-token을 이용하여 이후 결과를 가지고 올 수 있음 |
 

* `주의 사항`
    * 처음에는 next-token이 존재 할 수 없습니다.
    * token은 특정 시간이나 특정 조건에서 사라질 수 있습니다.
    * token 발행 시 limit은 고정됩니다.
* 시나리오 example)

1. 최초 query

 
<details>
<summary>요청 예</summary>
 
```shell script
curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces?limit={limit}'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>


2. "그룹 목록 응답 본문 data"에 포함된 next-token을 이용하여 요청
 

<details>
<summary>요청 예</summary>
 
```shell script
curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces?limit={limit}&next-token={next-token}'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>


* next-token이 존재하면 limit은 변경 될 수 없으며 token이 발행 될 때의 값으로 자동 세팅됨


#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | 얼굴 감지 모델 정보 |
| data.faceCount | int | O | 2 | 감지한 얼굴 수 |
| data.faces[].bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.faces[].bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.faces[].bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.faces[].bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.faces[].bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.faces[].confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.faces[].faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | 페이스 아이디 |
| data.faces[].imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 아이디<br>하나의 이미지 아이디에 여러 페이스 아이디가 존재 할 수 있음 |
| data.faces[].externalImageId | string |  | "image01.jpg" | 사용자가 이미지에 등록한 값 |
| data.nextToken | string | O | "dlkj-210jwoivndslko9d..." | paging에서 사용할 token<br>결과가 잘린 경우 next-token을 이용하여 이후 결과를 가지고 올 수 있음 |


<details>
<summary>응답 본문 예</summary>


```json
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

</details>

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-40030| NotFoundGroupError | 그룹 아이디를 찾을 수 없음 |
|-40040| InvalidTokenError | 잘못된 토큰 사용 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-50000| InternalServerError | 서버 에러 |

### 페이스 아이디로 얼굴 검색

* 페이스 아이디로 특정 그룹에서 얼굴을 검색하는 API입니다.
* 유사도가 가장 높은 순서로 일치하는 얼굴 정보의 배열을 반환합니다.

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id} |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |
| group-id | 사용자가 등록한 그룹 아이디<br>[a-z0-9-]{1,255} |
| face-id | 비교하려는 페이스 아이디 |
 
[URL Parameter]
 
| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| limit | int  | O  | 100  | 찾으려는 최대 값.<br>0 < limit <= 4096 |
| threshold | int  | O  | 90  | 매칭 여부를 판단하는 유사도 기준값<br>0 < threshold <= 100 |
 

<details>
<summary>요청 예</summary>
 
```shell script
curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}?limit={limit}&threshold'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>

#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.matchFaceCount | int | O | 2 | 감지한 얼굴 수 |
| data.matchFaces[].face.bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.matchFaces[].face.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.matchFaces[].face.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.matchFaces[].face.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.matchFaces[].face.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.matchFaces[].face.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.matchFaces[].face.faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | 페이스 아이디 |
| data.matchFaces[].face.imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 아이디<br>하나의 이미지 아이디에 여러 페이스 아이디가 존재 할 수 있음 |
| data.matchFaces[].face.externalImageId | string |  | "image01.jpg" | 사용자가 이미지에 등록한 값 |
| data.matchFaces[].similarity | float | O | 98.156 | 0\~100 값을 가지는 유사도 |


<details>
<summary>응답 본문 예</summary>

```json
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

</details>


#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-40030| NotFoundGroupError | 그룹 아이디를 찾을 수 없음 |
|-40050| NotFoundFaceIDError | 페이스 아이디를 찾을 수 없음 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-50000| InternalServerError | 서버 에러 |

### 이미지로 얼굴 검색

* 입력 이미지에서 감지한 가장 큰 얼굴을 사용하여 특정 그룹에 속한 얼굴과 일치 여부를 비교합니다.
* 입력 이미지는 base64로 인코딩된 이미지 바이트로 전달하거나 이미지 URL로 전달 할 수 있습니다.
* 입력 이미지에 대한 세부사항은 "[입력 이미지 가이드](#입력-이미지-가이드)"를 참고하시기 바랍니다.
* 유사도가 가장 높은 순서로 일치하는 얼굴 정보의 배열을 반환합니다.

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/search |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |
| group-id | 사용자가 등록한 group id<br>[a-z0-9-]{1,255} |
 
[URL Parameter]
 
| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| limit | int  | O  | 100  | 찾으려는 최대 값<br>0 < limit <= 4096 |
| threshold | int  | O  | 90  | 매칭 여부를 판단하는 유사도 기준값<br>0 < threshold <= 100 |

[Request Body]
 
| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| image.url | string |  | "https://..." | 이미지의 URL<br>image.url, image.bytes 중 반드시 1개만 있어야 함 |
| image.bytes | blob |  | "/0j3Ohdk==..." | base64로 인코딩된 이미지 바이트<br>image.url, image.bytes 중 반드시 1개만 있어야 함 |

* image.url, image.bytes 중 반드시 1개만 있어야 합니다.


<details>
<summary>요청 예</summary>
 
```shell script
curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/search?limit={limit}&threshold={threshold}'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8'  -d '{
    "image": {
        "url": "https://..."
    }
}'
```

</details>


#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.matchFaceCount | int | O | 2 | 감지한 얼굴 수 |
| data.matchFaces[].face.bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.matchFaces[].face.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.matchFaces[].face.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.matchFaces[].face.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.matchFaces[].face.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.matchFaces[].face.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.matchFaces[].face.faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | 페이스 아이디 |
| data.matchFaces[].face.imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | 이미지 아이디<br>하나의 이미지 아이디에 여러 페이스 아이디가 존재 할 수 있음 |
| data.matchFaces[].face.externalImageId | string |  | "image01.jpg" | 사용자가 이미지에 등록한 값 |
| data.matchFaces[].similarity | float | O | 98.156 | 0\~100 값을 가지는 유사도 |
| data.sourceFace.bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.sourceFace.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.sourceFace.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.sourceFace.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.sourceFace.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.sourceFace.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |


<details>
<summary>응답 본문 예</summary>

```json
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

</details>


#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-40030| NotFoundGroupError | 그룹 아이디를 찾을 수 없음 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-45020| ImageTooLargeException | 이미지 크기 초과 |
|-45040| InvalidImageFormatException | 지원하지 않는 이미지 포맷 |
|-45050| InvalidImageURLException | 잘못된 이미지 URL |
|-45060| ImageTimeoutError | 이미지 다운로드 시간 초과 |
|-50000| InternalServerError | 서버 에러 |

### 얼굴 비교

* 기준 이미지(sourceImage)와 비교 이미지(targetImage)에서 감지한 얼굴이 얼마나 유사한지 비교합니다.
* 기준 이미지에서 감지한 얼굴 중 가장 큰 얼굴(기준 얼굴)만 사용합니다.
* 입력 이미지는 base64로 인코딩된 이미지 바이트로 전달하거나 이미지 URL로 전달할 수 있습니다.
* 입력 이미지에 대한 세부사항은 "[입력 이미지 가이드](#입력-이미지-가이드)"를 참고하시기 바랍니다.
* 유사도가 가장 높은 순서로 일치하는 얼굴 정보의 배열을 반환합니다.

#### 요청
[URI]
 
| 메서드 | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/compare |
 
[Path Variable]
 
| 이름 | 설명 |
| --- | --- |
| appKey | 사용자마다 발급된 서비스용 키 |
 
[Request Body]
 
| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| sourceImage | object | O | - | 얼굴 비교 시 기준이 되는 이미지<br/>(=referenceImage) |
| sourceImage.url | string |  | "https://..." | 이미지의 URL<br>image.url, image.bytes 중 반드시 1개만 있어야 함 |
| sourceImage.bytes | blob |  | "/0j3Ohdk==..." | base64로 인코딩된 이미지 바이트<br>image.url, image.bytes 중 반드시 1개만 있어야 함 |
| targetImage | object | O | - | 비교 대상이 되는 얼굴이 포함된 이미지<br/>(=comparisonImage) |
| targetImage.url | string |  | "https://..." | 이미지의 URL<br>image.url, image.bytes 중 반드시 1개만 있어야 함 |
| targetImage.bytes | blob |  | "/0j3Ohdk==..." | base64로 인코딩된 이미지 바이트<br>image.url, image.bytes 중 반드시 1개만 있어야 함 |
| threshold | int | O | 90 | 매칭 여부를 판단하는 유사도 기준값<br>0 < threshold <= 100 |


<details>
<summary>요청 예</summary>
 
```shell script
curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/compare'  -H 'Authorization: {secretKey}'  -H 'Content-Type: application/json;charset=UTF-8'  -d '{
    "sourceImage": {
        "url": "https://..."
    },
    "targetImage": {
        "url": "https://..."
    },
    "threshold ": 80
}'
```

</details>


#### 응답

* [응답 본문 header 설명 생략]
    * [응답 공통 정보](#응답-공통-정보)에서 확인 가능

[응답 본문 data]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | 얼굴 감지 모델 정보 |
| data.matchedFaceDetailCount | int | O | 1 | 매칭된 얼굴 수 |
| data.matchedFaceDetails[].faceDetail.bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.matchedFaceDetails[].faceDetail.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.matchedFaceDetails[].faceDetail.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.matchedFaceDetails[].faceDetail.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.matchedFaceDetails[].faceDetail.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.matchedFaceDetails[].faceDetail.landmarks | array | O | - | 얼굴 특징 |
| data.matchedFaceDetails[].faceDetail.landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.matchedFaceDetails[].faceDetail.landmarks[].x | float | O | 0.362 | 얼굴 특징의 x 좌표 |
| data.matchedFaceDetails[].faceDetail.landmarks[].y | float | O | 0.362 | 얼굴 특징의 y 좌표 |
| data.matchedFaceDetails[].faceDetail.orientation | object | O | 0.362 | 얼굴 방향의 좌표 |
| data.matchedFaceDetails[].faceDetail.orientation.x | float | O | 15.303436 | 얼굴 방향의 x 좌표 |
| data.matchedFaceDetails[].faceDetail.orientation.y | float | O | -9.222179 | 얼굴 방향의 y 좌표 |
| data.matchedFaceDetails[].faceDetail.orientation.z | float | O | -7.97249 | 얼굴 방향의 z 좌표 |
| data.matchedFaceDetails[].faceDetail.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.matchedFaceDetails[].similarity | float | O | 98.156 | 0\~100 값을 가지는 유사도 |
| data.unmatchedFaceDetailCount | int | O | 1 | 매칭되지 않은 얼굴 수 |
| data.unmatchedFaceDetails[].faceDetail.bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.unmatchedFaceDetails[].faceDetail.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.unmatchedFaceDetails[].faceDetail.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.unmatchedFaceDetails[].faceDetail.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.unmatchedFaceDetails[].faceDetail.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.unmatchedFaceDetails[].faceDetail.landmarks | array | O | - | 얼굴 특징 |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].type | string | O | "leftEye" | 유효한 값 목록:<br>`leftEye`, `rightEye`, `nose`, `leftLip`, `rightLib` |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].x | float | O | 0.362 | 얼굴 특징의 x 좌표 |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].y | float | O | 0.362 | 얼굴 특징의 y 좌표 |
| data.unmatchedFaceDetails[].faceDetail.orientation | object | O | 0.362 | 얼굴 방향의 좌표 |
| data.unmatchedFaceDetails[].faceDetail.orientation.x | float | O | 15.303436 | 얼굴 방향의 x 좌표 |
| data.unmatchedFaceDetails[].faceDetail.orientation.y | float | O | -9.222179 | 얼굴 방향의 y 좌표 |
| data.unmatchedFaceDetails[].faceDetail.orientation.z | float | O | -7.97249 | 얼굴 방향의 z 좌표 |
| data.unmatchedFaceDetails[].faceDetail.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |
| data.unmatchedFaceDetails[].similarity | float | O | 98.156 | 0\~100 값을 가지는 유사도 |
| data.sourceFace.bbox | object | O | - | 이미지 내에서 감지한 얼굴 box 정보 |
| data.sourceFace.bbox.x0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x0 좌표 |
| data.sourceFace.bbox.y0 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y0 좌표 |
| data.sourceFace.bbox.x1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 x1 좌표 |
| data.sourceFace.bbox.y1 | float | O | 0.123 | 이미지 내에서 감지한 얼굴 box의 y1 좌표 |
| data.sourceFace.confidence | float | O | 99.9123 | 얼굴 인식 신뢰도 |


<details>
<summary>응답 본문 예</summary>

```json
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
                "orientation": {
                    "x": 15.303436,
                    "y": -9.222179,
                    "z": -7.97249
                },
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
                "orientation": {
                    "x": 15.303436,
                    "y": -9.222179,
                    "z": -7.97249
                },
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
                    "orientation": {
                        "x": 15.303436,
                        "y": -9.222179,
                        "z": -7.97249
                    },
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

</details>


#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
|-40000| InvalidParam | 파라미터에 오류가 있음 |
|-41000| UnauthorizedAppKey | 승인되지 않은 appKey |
|-45020| ImageImageTooLargeException:{Source/Target} | {Source/Target} Image: 이미지 크기 초과 |
|-45040| ImageInvalidImageFormatException:{Source/Target} | {Source/Target} image: 지원하지 않는 이미지 포맷 |
|-45050| ImageInvalidImageURLException:{Source/Target} | {Source/Target} image: 잘못된 이미지 URL |
|-45060| ImageImageTimeoutError:{Source/Target} | {Source/Target} image: 이미지 다운로드 시간 초과 |
|-50000| InternalServerError | 서버 에러 |
