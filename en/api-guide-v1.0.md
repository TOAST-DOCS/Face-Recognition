## AI Service > Face Recognition > API v1.0 Guide

* This document describes the APIs required for using Face Recognition API v1.0.

## Common API Information
### Preparations
- To use APIs, an integrated project Appkey or service Appkey is required.  
    - We recommend using the integrated project Appkey.
    - You can create and use the integrated project Appkey from the API security settings in the project settings page.
    - The service Appkey is located in the **URL & Appkey** menu on the top of the console.




### Common Request Information
- The security key needs to be authenticated in order to use APIs.

[API domain]

| Domain |
| --- |
| https://face-recognition.api.nhncloudservice.com |

<span id="input-image-guide"></span>
### Input Image Guide

* Face images must be at least 80*80 px in width and height.
    * The face size must be at least 60*60 px to be eligible for facial recognition.
    * As the image gets bigger, the minimum face size must get bigger as well for better facial recognition precision.
    * The bigger the proportion of the face area in the image, the more precise the facial recognition.
* In the input image, both left/right angle(Yaw) and up/down angle(Pitch) of the face must be 45 degrees or less.
* Max image size: up to 3MB(3,000,000Byte)
* Supported image formats: PNG, JPEG
* If you specify the port directly in the image URL, only ports 80, 443, 10000-12000 can be used.

<span id="common-response"></span>
### Common Response Information

- Returns '200 OK' for all API requests. For more information on the response results, see Response Body Header.

[Response Body Header]

| Name | Type | Description |
| --- | --- | --- |
| header.isSuccessful | boolean | true: normal<br>false: error |
| header.resultCode | int | 0: normal<br>bigger than 0: partial success<br>negative number: error |
| header.resultMessage | string | "Success": normal<br>other: returns error message |

[Success response body example]

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```

[Failure response body example]

```json
{
    "header": {
        "isSuccessful": false,
        "resultCode": -40000,
        "resultMessage": "InvalidParam"
    }
}
```
## API Contents

### Create Groups

- This API creates groups. You can use [Register Face](./api-guide-v1.0/#add-face) to a created group to register faces.

#### Request
[URI]

| Method | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |

[Request Body]

| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| groupId | string | O |  | [a-z0-9-]{1,255} | "my-group" | Group ID registered by user |


<details>
<summary>Request example</summary>

```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "groupId": "my-group"
}'
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)


<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
        "isSuccessful": true
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40010| InvalidGroupID | Group ID error |
|-40020| DuplicatedGroupID | Duplicate group ID |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-50000| InternalServerError | Server error |

### Group List

* This API views the list of groups.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups |


[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |

[URL Parameter]

| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| limit | int | O |  | 1 ~ 200 | 100 | Max size |
| next-token | string |  |  |  | "skljsdioew..."  | Value returned from 'Group list response body data'<br/> If the result is partially truncated, the next-token can be used to bring the rest of the result |


* Caution
    * In the beginning, the next-token cannot exist.
    * The token may disappear at a specific time or under specific conditions.
    * Upon issuing the token, the limit becomes fixed.
* Scenario example)

* Initial query


<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups?limit={limit}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

* Requested using the next-token contained in the 'Group list response body data'


<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups?limit={limit}&next-token={next-token}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

* If the next-token exists, the limit cannot be changed, and it is auto-set to the value from when the token was issued

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)


[Response body data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.groupCount | int | O | 2 | Number of groups |
| data.groups[].groupId | string | O | "group-id" | Group IDs registered by user |
| data.groups[].modelVersion | string | O | "v1.0" | Face recognition model version info |
| data.nextToken | string | O | "dlkj-210jwoivndslko9d..." | Token to be used for paging<br>If the result is partially truncated, the next-token can be used to bring the rest of the result |

<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
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

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40040| InvalidTokenError | Invalid token used |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-50000| InternalServerError | Server error |

### Group Details

* This API views details of a specific group, such as group ID, model version, number of faces registered in the group, etc.

#### Request
[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]{1,255} |

<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)

[Response body data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.groupCount | int | O | 1 | Number of groups |
| data.groups[].groupId | string | O | "group-id" | Group IDs registered by user |
| data.groups[].modelVersion | string | O | "v1.0" | Face recognition model version info |
| data.groups[].createTime | string | O | "2020-11-04T12:36:24" | Time when the group was created |
| data.groups[].faceCount | int |  | 365 | Number of faces registered in the group |

<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
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

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40030| NotFoundGroupError | Could not find the group ID |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-50000| InternalServerError | Server error |

### Delete Group

* This API permanently deletes a group and all face information of the group.

#### Request

[URI]

| Method | URI |
| --- | --- |
| DELETE | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]{1,255} |


<details>
<summary>Request example</summary>

```
$ curl -X DELETE '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}' -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>



#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)

<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
        "isSuccessful": true
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40030| NotFoundGroupError | Could not find the group ID |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-50000| InternalServerError | Server error |

<span id="detect-face"></span>
### Recognize Face

* This API recognizes faces from input image.
* Returns the position data of the face, eyes, nose, and moth and the confidence value from the recognized face.
* Recognizes up to 20 faces from the input image in the order from the largest to smallest face.
* The input image can be delivered via Base64-encoded image bytes or image url.
* To find out more about input image, see [Input Image Guide](./api-guide-v1.0/#input-image-guide).

<span id="detect-face-request"></span>
#### Request
[URI]
 
| Method | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/detect |

[Path Variable]
 
| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |

[Request Body]

| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| image | object | O |  |  | - | Image to use for face recognition |
| image.url | string | △ |  |  | "https://..." | Image URL |
| image.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| orientation | bool |  | true | true, false | false | Whether to use face orientation recognition function |
| mask | bool |  | true | true, false | false | Whether to use the mask wear recognition function |

* Must have only one of either image.url or image.bytes.


<details>
<summary>Request example</summary>
 
```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/detect' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "image": {
        "url":"https://..."
    }
}'
```

</details>

<span id="detect-face-response"></span>
#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)

[Response body data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.faceDetailCount | int | O | 1 | Number of faces recognized |
| data.faceDetails[].bbox | object | O | - | Bounding box information of a face detected in the image |
| data.faceDetails[].bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.faceDetails[].bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.faceDetails[].bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.faceDetails[].bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.faceDetails[].landmarks | array | O | - | Facial characteristics |
| data.faceDetails[].landmarks[].type | string | O | "leftEye" | List of valid values:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.faceDetails[].landmarks[].y | float | O | 0.362 | y coordinate of the facial characteristic |
| data.faceDetails[].landmarks[].x | float | O | 0.362 | x coordinate of the facial characteristic |
| data.faceDetails[].orientation | object |  | 0.362 | Face angle |
| data.faceDetails[].orientation.x | float |  | 15.303436 | Face left/right angle(Yaw) |
| data.faceDetails[].orientation.y | float |  | -9.222179 | Face up/down angle(Pitch) |
| data.faceDetails[].orientation.z | float |  | -7.97249 | Face angle compared to level surface(Roll) |
| data.faceDetails[].mask | boolean |  | false | Whether to wear a mask |
| data.faceDetails[].confidence | float | O | 99.9123 | Face recognition confidence |


<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
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
            "mask": false,
            "confidence": 99.8945155187
        }]
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-45020| ImageTooLargeException | Image size exceeded |
|-45030| InvalidImageParameterException | Invalid image parameter. Mainly due to incorrect Base64 encoding |
|-45040| InvalidImageFormatException | Unsupported image format |
|-45050| InvalidImageURLException | Invalid image URL |
|-45060| ImageTimeoutError | Image download timeout |
|-50000| InternalServerError | Server error |

<span id="add-face"></span>
### Register face

* This API registers the face recognized from the input image to a certain group.
* Recognizes the face box from the input image, and extracts the facial characteristics from the face box as vectors. As for the input image and the face recognized from the input image, neither is saved.
* Extracted vector data gets saved in the database after encryption.
* The saved vector data gets used as characteristic vectors for the [Search face by face ID](./api-guide-v1.0/#search-by-face-id)and [Search face by image](./api-guide-v1.0/#search-by-image) APIs.
* The input image can be delivered via Base64-encoded image bytes or image url.
* To find out more about input image, see [Input Image Guide](./api-guide-v1.0/#input-image-guide).
* 'imageId' is a value given for the input image, and the 'externalImageId' is a value which can be directly given by the user. The user can utilize 'imageId' and 'externalImageId' to perform labeling for the image or face ID from the user-end, and they can also be used on their own like indexes.
* 'imageId' and 'externalImageId' are returned from the response of the [Face list within a group](./api-guide-v1.0/#face-list-in-a-group) and [Search face by face ID](./api-guide-v1.0/#search-by-face-id) and [Search face by image](./api-guide/#search-by-image) APIs. 
* Up to 100,000 faces can be registered per single group.
 
<span id="add-face-request"></span>
#### Request

[URI]
 
| Method | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id} |
 
[Path Variable]
 
| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]{1,255} |
 
[Request Body]

| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| image | object | O |  |  | - | Image to use for face recognition |
| image.url | string | △ |  |  | "https://..." | Image URL |
| image.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| externalImageId | string |  |  | [a-z0-9-]{1,255} | "image01.jsp" | Values provided by user to create a label for an image or face ID |
| limit | int | O |  | 1 ~ 20 | 3 | Max number of faces to register in the group after sorting the faces recognized from the input image in order of largest to smallest |
| orientation | bool |  | true | true, false | false | Whether to use face orientation recognition function |
| mask | bool |  | true | true, false | false | Whether to use the mask wear recognition function |

* Must have only one of either image.url or image.bytes.


<details>
<summary>Request example</summary>
 
```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "image": {
        "url": "https://..."
    },
    "externalImageId": "image01.jpg",
    "limit": 3
}'
```

</details>

<span id="add-face-response"></span>
#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)

[Response body data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | Face recognition model info |
| data.addedFaceCount | int | O | 1 | Number of faces registered |
| data.addedFaces[].bbox | object | O | - | Bounding box information of a face detected in the image |
| data.addedFaces[].bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.addedFaces[].bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.addedFaces[].bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.addedFaces[].bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.addedFaces[].faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | Face ID |
| data.addedFaces[].imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | Image ID<br>There can be multiple face IDs per image ID |
| data.addedFaces[].externalImageId | string |  | "image01.jpg" | Values delivered by request |
| data.addedFaces[].confidence | float | O | 99.9123 | Face recognition confidence |
| data.addedFaceDetails[].bbox | object | O | - | Bounding box information of a face detected in the image |
| data.addedFaceDetails[].bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.addedFaceDetails[].bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.addedFaceDetails[].bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.addedFaceDetails[].bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.addedFaceDetails[].landmarks | array | O | - | Facial characteristics |
| data.addedFaceDetails[].landmarks[].type | string | O | "leftEye" | List of valid values:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.addedFaceDetails[].landmarks[].y | float | O | 0.362 | y coordinate of the facial characteristic |
| data.addedFaceDetails[].landmarks[].x | float | O | 0.362 | x coordinate of the facial characteristic |
| data.addedFaceDetails[].orientation | object |  | 0.362 | Face angle |
| data.addedFaceDetails[].orientation.x | float |  | 15.303436 | Face left/right angle(Yaw) |
| data.addedFaceDetails[].orientation.y | float |  | -9.222179 | Face up/down angle(Pitch) |
| data.addedFaceDetails[].orientation.z | float |  | -7.97249 | Face angle compared to level surface(Roll) |
| data.addedFaceDetails[].mask | boolean |  | false | Whether to wear a mask |
| data.addedFaceDetails[].confidence | float | O | 99.9123 | Face recognition confidence |
| data.notAddedFaceCount | int | O | 1 | Number of faces not registered |
| data.notAddedFaces[].bbox | object | O | - | Bounding box information of a face detected in the image |
| data.notAddedFaces[].bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.notAddedFaces[].bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.notAddedFaces[].bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.notAddedFaces[].bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.notAddedFaces[].landmarks | array | O | - | Facial characteristics |
| data.notAddedFaces[].landmarks[].type | string | O | "leftEye" | List of valid values:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.notAddedFaces[].landmarks[].x | float | O | 0.362 | x coordinate of the facial characteristic |
| data.notAddedFaces[].landmarks[].y | float | O | 0.362 | y coordinate of the facial characteristic |
| data.notAddedFaces[].orientation | object |  | 0.362 | Face angle |
| data.notAddedFaces[].orientation.x | float |  | 15.303436 | Face left/right angle(Yaw) |
| data.notAddedFaces[].orientation.y | float |  | -9.222179 | Face up/down angle(Pitch) |
| data.notAddedFaces[].orientation.z | float |  | -7.97249 | Face angle compared to level surface(Roll) |
| data.notAddedFaces[].mask | boolean |  | false | Whether to wear a mask |
| data.notAddedFaces[].confidence | float | O | 99.9123 | Face recognition confidence |

* data.addedFacesDetails is the detailed information of data.addedFaces, which does not include duplicate elements and is not stored separately.

<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
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
            "mask": false,
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
            "mask": false,
            "confidence": 99.8945155187

        }]

    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
|40070| ServiceQuotaExceededPartialSuccessException | Not all faces were registered because the max number of faces allowed for a single group has been exceeded |
|-40000| InvalidParam | The parameter contains an error |
|-40030| NotFoundGroupError | Could not find the group ID |
|-40070| ServiceQuotaExceededException | Exceeds the max number of faces which can be registered for a single group |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-45020| ImageTooLargeException | Image size exceeded |
|-45030| InvalidImageParameterException | Invalid image parameter. Mainly due to incorrect Base64 encoding |
|-45040| InvalidImageFormatException | Unsupported image format |
|-45050| InvalidImageURLException | Invalid image URL |
|-45060| ImageTimeoutError | Image download timeout |
|-50000| InternalServerError | Server error |

### Delete Face

* This API deletes specific registered faces from the group.

#### Request
[URI]
 
| Method | URI |
| --- | --- |
| DELETE | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id} |
 
[Path Variable]
 
| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]{1,255} |
| face-id | Registered face ID |
 

<details>
<summary>Request example</summary>

```
$ curl -X DELETE '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}' -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)


<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
        "isSuccessful": true
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40030| NotFoundGroupError | Could not find the group ID |
|-40050| NotFoundFaceIDError | Could not find the face ID |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-50000| InternalServerError | Server error |

<span id="face-list-in-a-group"></span>
### List of Faces within Group

* This API views the face info list registered for a specific group.
* Returns the face info array in order of most recently registered.

#### Request
[URI]
 
| Method | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces |
 
[Path Variable]
 
| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]{1,255} |
 
[URL Parameter]

| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| limit | int | O |  | 1 ~ 200 | 100 | Max size |
| next-token | string |  |  |  | "skljsdioew..."  | Value returned from 'Group list response body data'<br/> If the result is partially truncated, the next-token can be used to bring the rest of the result |
 

* Caution
    * In the beginning, the next-token cannot exist.
    * The token may disappear at a specific time or under specific conditions.
    * Upon issuing the token, the limit becomes fixed.
* Scenario example)

* Initial query

 
<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces?limit={limit}' -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>


* Requested using the next-token contained in the 'Group list response body data'
 

<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces?limit={limit}&next-token={next-token}' -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>


* If the next-token exists, the limit cannot be changed, and it is auto-set to the value from when the token was issued


#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)

[Response body data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | Face recognition model info |
| data.faceCount | int | O | 2 | Number of faces registered in the group |
| data.faces[].bbox | object | O | - | Bounding box information of a face in the image used for face registration |
| data.faces[].bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.faces[].bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.faces[].bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.faces[].bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.faces[].confidence | float | O | 99.9123 | Recognition confidence of a face in the image used for face registration |
| data.faces[].faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | Face ID |
| data.faces[].imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | Image ID<br>There can be multiple face IDs per image ID |
| data.faces[].externalImageId | string |  | "image01.jpg" | Values registered in the image by user |
| data.nextToken | string | O | "dlkj-210jwoivndslko9d..." | Token to be used for paging<br>If the result is partially truncated, the next-token can be used to bring the rest of the result |


<details>
<summary>Response body example</summary>


```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
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

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40030| NotFoundGroupError | Could not find the group ID |
|-40040| InvalidTokenError | Invalid token used |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-50000| InternalServerError | Server error |

<span id="search-by-face-id"></span>
### Search face by face ID

* This API searches for faces from a specific group using the face ID.
* Returns the array of the face info in order of the most to least similar.

#### Request
[URI]
 
| Method | URI |
| --- | --- |
| GET | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id} |
 
[Path Variable]
 
| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]{1,255} |
| face-id | Face ID to compare |
 
[URL Parameter]
 
| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| limit | int | O |  | 1 ~ 4096 | 100 | Max value to find |
| threshold | int | O |  | 1 ~ 100 | 90 | A similarity reference value that determines a match |


<details>
<summary>Request example</summary>
 
```
$ curl -X GET '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}?limit={limit}&threshold={threshold}' -H 'Content-Type: application/json;charset=UTF-8' 
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)

[Response body data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.matchFaceCount | int | O | 2 | Number of faces matching the largest face detected in the input image |
| data.matchFaces[].face.bbox | object | O | - | Bounding box information of a face in the image used for face registration |
| data.matchFaces[].face.bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.matchFaces[].face.bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.matchFaces[].face.bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.matchFaces[].face.bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.matchFaces[].face.confidence | float | O | 99.9123 | Recognition confidence of a face in the image used for face registration |
| data.matchFaces[].face.faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | Face ID |
| data.matchFaces[].face.imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | Image ID<br>There can be multiple face IDs per image ID |
| data.matchFaces[].face.externalImageId | string |  | "image01.jpg" | Values registered in the image by user |
| data.matchFaces[].similarity | float | O | 98.156 | Similarity value between 0 and 100 |


<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
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

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40030| NotFoundGroupError | Could not find the group ID |
|-40050| NotFoundFaceIDError | Could not find the face ID |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-50000| InternalServerError | Server error |

<span id="search-by-image"></span>
### Search face by image

* Uses the largest face recognized from the input image to compare if it matches a face from a specific group.
* The input image can be delivered via Base64-encoded image bytes or image url.
* To find out more about input image, see [Input Image Guide](./api-guide-v1.0/#input-image-guide).
* Returns the array of the face info in order of the most to least similar.

<span id="search-by-image-request"></span>
#### Request
[URI]
 
| Method | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/search |
 
[Path Variable]
 
| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group id registered by user<br>[a-z0-9-]{1,255} |
 
[URL Parameter]
 
| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| limit | int | O |  | 1 ~ 4096 | 100 | Max value to find |
| threshold | int | O |  | 1 ~ 100 | 90 | A similarity reference value that determines a match |

[Request Body]
 
| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| image | object | O |  |  | - | Image to use for face recognition |
| image.url | string | △ |  |  | "https://..." | Image URL |
| image.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| orientation | bool |  | true | true, false | false | Whether to use face orientation recognition function |
| mask | bool |  | true | true, false | false | Whether to use the mask wear recognition function |

* Must have only one of either image.url or image.bytes.


<details>
<summary>Request example</summary>
 
```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/groups/{group-id}/search?limit={limit}&threshold={threshold}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "image": {
        "url": "https://..."
    }
}'
```

</details>


#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)

[Response body data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.matchFaceCount | int | O | 2 | Number of faces matching the largest face detected in the input image |
| data.matchFaces[].face.bbox | object | O | - | Bounding box information of a face in the image used for face registration |
| data.matchFaces[].face.bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.matchFaces[].face.bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.matchFaces[].face.bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.matchFaces[].face.bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.matchFaces[].face.confidence | float | O | 99.9123 | Recognition confidence of a face in the image used for face registration |
| data.matchFaces[].face.faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | Face ID |
| data.matchFaces[].face.imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | Image ID<br>There can be multiple face IDs per image ID |
| data.matchFaces[].face.externalImageId | string |  | "image01.jpg" | Values registered in the image by user |
| data.matchFaces[].similarity | float | O | 98.156 | Similarity value between 0 and 100 |
| data.sourceFace.bbox | object | O | - | Bounding box information of the largest face detected in the input image |
| data.sourceFace.bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.sourceFace.bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.sourceFace.bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.sourceFace.bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.sourceFace.landmarks | array | O | - | Facial characteristics |
| data.sourceFace.landmarks[].type | string | O | "leftEye" | List of valid values:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.sourceFace.landmarks[].y | float | O | 0.362 | y coordinate of the facial characteristic |
| data.sourceFace.landmarks[].x | float | O | 0.362 | x coordinate of the facial characteristic |
| data.sourceFace.orientation | object |  | 0.362 | Face angle |
| data.sourceFace.orientation.x | float |  | 15.303436 | Face left/right angle(Yaw) |
| data.sourceFace.orientation.y | float |  | -9.222179 | Face up/down angle(Pitch) |
| data.sourceFace.orientation.z | float |  | -7.97249 | Face angle compared to level surface(Roll) |
| data.sourceFace.mask | boolean |  | false | Whether to wear a mask |
| data.sourceFace.confidence | float | O | 99.9123 | Recognition confidence of the largest face detected in the input image |


<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
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
                "x0": 0.26785714285714285,
                "y0": 0.22767857142857142,
                "x1": 0.7366071428571429,
                "y1": 0.8660714285714286
            },
            "landmarks": [
                {
                    "type": "leftEye",
                    "x": 0.39285714285714285,
                    "y": 0.47767857142857145
                },
                {
                    "type": "rightEye",
                    "x": 0.6071428571428571,
                    "y": 0.4732142857142857
                },
                {
                    "type": "nose",
                    "x": 0.5,
                    "y": 0.6026785714285714
                },
                {
                    "type": "leftLip",
                    "x": 0.41964285714285715,
                    "y": 0.7276785714285714
                },
                {
                    "type": "rightLip",
                    "x": 0.5758928571428571,
                    "y": 0.7276785714285714
                }
            ],
            "orientation": {
                "x": 1.400425,
                "y": 6.624787,
                "z": -2.08028
            },
            "mask": false,
            "confidence": 0.999894
        }
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40030| NotFoundGroupError | Could not find the group ID |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-45020| ImageTooLargeException | Image size exceeded |
|-45030| InvalidImageParameterException | Invalid image parameter. Mainly due to incorrect Base64 encoding |
|-45040| InvalidImageFormatException | Unsupported image format |
|-45050| InvalidImageURLException | Invalid image URL |
|-45060| ImageTimeoutError | Image download timeout |
|-50000| InternalServerError | Server error |

<span id="compare-face"></span>
### Compare faces

* Compares the similarity of the faces recognized from the reference image(sourceImage) and comparison image(targetImage).
* Out of the faces recognized from the reference image, only the largest face(source face) is used.
* The input image can be delivered via Base64-encoded image bytes or image url.
* To find out more about input image, see [Input Image Guide](./api-guide-v1.0/#input-image-guide).
* Returns the array of the face info in order of the most to least similar.

<span id="compare-face-request"></span>
#### Request
[URI]
 
| Method | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/compare |
 
[Path Variable]
 
| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
 
[URL Parameter]
 
| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| threshold | int | O |  | 1 ~ 100 | 90 | A similarity reference value that determines a match |

[Request Body]
 
| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| sourceImage | object | O |  |  | - | Reference image for facial comparison <br/>(=referenceImage) |
| sourceImage.url | string | △ |  |  | "https://..." | Image URL |
| sourceImage.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| targetImage | object | O |  |  | - | Image containing the target face for comparison <br/>(=comparisonImage) |
| targetImage.url | string | △ |  |  | "https://..." | Image URL |
| targetImage.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| orientation | bool |  | true | true, false | false | Whether to use face orientation recognition function |
| mask | bool |  | true | true, false | false | Whether to use the mask wear recognition function |

* Must have only one of either sourceImage.url or sourceImage.bytes.
* Must have only one of either targetImage.url or targetImage.bytes.

<details>
<summary>Request example</summary>
 
```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/compare?threshold={threshold}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "sourceImage": {
        "url": "https://..."
    },
    "targetImage": {
        "url": "https://..."
    }
}'
```

</details>

<span id="compare-face-response"></span>
#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)

[Response body data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.modelVersion | string | O | "v1.0" | Face recognition model info |
| data.matchedFaceDetailCount | int | O | 1 | Number of matched faces |
| data.matchedFaceDetails[].faceDetail.bbox | object | O | - | Bounding box information of a face detected in the image |
| data.matchedFaceDetails[].faceDetail.bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.matchedFaceDetails[].faceDetail.bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.matchedFaceDetails[].faceDetail.bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.matchedFaceDetails[].faceDetail.bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.matchedFaceDetails[].faceDetail.landmarks | array | O | - | Facial characteristics |
| data.matchedFaceDetails[].faceDetail.landmarks[].type | string | O | "leftEye" | List of valid values:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.matchedFaceDetails[].faceDetail.landmarks[].x | float | O | 0.362 | x coordinate of the facial characteristic |
| data.matchedFaceDetails[].faceDetail.landmarks[].y | float | O | 0.362 | y coordinate of the facial characteristic |
| data.matchedFaceDetails[].faceDetail.orientation | object |  | 0.362 | Face angle |
| data.matchedFaceDetails[].faceDetail.orientation.x | float |  | 15.303436 | Face left/right angle(Yaw) |
| data.matchedFaceDetails[].faceDetail.orientation.y | float |  | -9.222179 | Face up/down angle(Pitch) |
| data.matchedFaceDetails[].faceDetail.orientation.z | float |  | -7.97249 | Face angle compared to level surface(Roll) |
| data.matchedFaceDetails[].mask | boolean |  | false | Whether to wear a mask |
| data.matchedFaceDetails[].faceDetail.confidence | float | O | 99.9123 | Face recognition confidence |
| data.matchedFaceDetails[].similarity | float | O | 98.156 | Similarity value between 0 and 100 |
| data.unmatchedFaceDetailCount | int | O | 1 | Number of unmatched faces |
| data.unmatchedFaceDetails[].faceDetail.bbox | object | O | - | Bounding box information of a face detected in the image |
| data.unmatchedFaceDetails[].faceDetail.bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.unmatchedFaceDetails[].faceDetail.bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.unmatchedFaceDetails[].faceDetail.bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.unmatchedFaceDetails[].faceDetail.bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.unmatchedFaceDetails[].faceDetail.landmarks | array | O | - | Facial characteristics |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].type | string | O | "leftEye" | List of valid values:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].x | float | O | 0.362 | x coordinate of the facial characteristic |
| data.unmatchedFaceDetails[].faceDetail.landmarks[].y | float | O | 0.362 | y coordinate of the facial characteristic |
| data.unmatchedFaceDetails[].faceDetail.orientation | object |  | 0.362 | Face angle |
| data.unmatchedFaceDetails[].faceDetail.orientation.x | float |  | 15.303436 | Face left/right angle(Yaw) |
| data.unmatchedFaceDetails[].faceDetail.orientation.y | float |  | -9.222179 | Face up/down angle(Pitch) |
| data.unmatchedFaceDetails[].faceDetail.orientation.z | float |  | -7.97249 | Face angle compared to level surface(Roll) |
| data.unmatchedFaceDetails[].mask | boolean |  | false | Whether to wear a mask |
| data.unmatchedFaceDetails[].faceDetail.confidence | float | O | 99.9123 | Face recognition confidence |
| data.unmatchedFaceDetails[].similarity | float | O | 98.156 | Similarity value between 0 and 100 |
| data.sourceFace.bbox | object | O | - | Bounding box information of the largest face detected in the input image |
| data.sourceFace.bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.sourceFace.bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.sourceFace.bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.sourceFace.bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.sourceFace.landmarks | array | O | - | Facial characteristics |
| data.sourceFace.landmarks[].type | string | O | "leftEye" | List of valid values:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.sourceFace.landmarks[].y | float | O | 0.362 | y coordinate of the facial characteristic |
| data.sourceFace.landmarks[].x | float | O | 0.362 | x coordinate of the facial characteristic |
| data.sourceFace.orientation | object |  | 0.362 | Face angle |
| data.sourceFace.orientation.x | float |  | 15.303436 | Face left/right angle(Yaw) |
| data.sourceFace.orientation.y | float |  | -9.222179 | Face up/down angle(Pitch) |
| data.sourceFace.orientation.z | float |  | -7.97249 | Face angle compared to level surface(Roll) |
| data.sourceFace.mask | boolean |  | false | Whether to wear a mask |
| data.sourceFace.confidence | float | O | 99.9123 | Recognition confidence of the largest face detected in the input image |


<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "Success",
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
                "mask": false,
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
                "mask": false,
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
                    "mask": false,
                    "confidence": 99.8945155187
                },
                "similarity": 60.654
            }

        ],
        "sourceFace": {
            "bbox": {
                "x0": 0.26785714285714285,
                "y0": 0.22767857142857142,
                "x1": 0.7366071428571429,
                "y1": 0.8660714285714286
            },
            "landmarks": [
                {
                    "type": "leftEye",
                    "x": 0.39285714285714285,
                    "y": 0.47767857142857145
                },
                {
                    "type": "rightEye",
                    "x": 0.6071428571428571,
                    "y": 0.4732142857142857
                },
                {
                    "type": "nose",
                    "x": 0.5,
                    "y": 0.6026785714285714
                },
                {
                    "type": "leftLip",
                    "x": 0.41964285714285715,
                    "y": 0.7276785714285714
                },
                {
                    "type": "rightLip",
                    "x": 0.5758928571428571,
                    "y": 0.7276785714285714
                }
            ],
            "orientation": {
                "x": 1.400425,
                "y": 6.624787,
                "z": -2.08028
            },
            "mask": false,
            "confidence": 0.999894
        }
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-45020| ImageImageTooLargeException:{Source/Target} | {Source/Target} Image: Image size exceeded |
|-45030| InvalidImageParameterException:{Source/Target} | Invalid image parameter. Mainly due to incorrect Base64 encoding |
|-45040| ImageInvalidImageFormatException:{Source/Target} | {Source/Target} image: Unsupported image format |
|-45050| ImageInvalidImageURLException:{Source/Target} | {Source/Target} image: Invalid image URL |
|-45060| ImageImageTimeoutError:{Source/Target} | {Source/Target} Image: Image download timeout |
|-50000| InternalServerError | Server error |


<span id="verify"></span>
### Face Verification
* This function compares the face ID of a specific face registered in advance with the face detected in the input image and returns a similarity value.
* Use [Register Face](./api-guide-v1.0/#add-face) to a created group to register faces.
* Only the largest face detected in the input image is used.  
* The input image can be delivered via Base64-encoded image bytes or image url.
* To find out more about input image, see [Input Image Guide](./api-guide-v1.0/#input-image-guide).

<span id="verify-request"></span>
#### Request
[URI]

| Method | URI |
| --- | --- |
| POST | /nhn-face-reco/v1.0/appkeys/{appKey}/verify/groups/{group-id}/faces/{face-id} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]{1,255} |
| face-id | Registered face ID |

[Request Body]

| Name | Type | Required | Default value | Valid range | Example | Description |
| --- | --- | --- | --- | --- | --- | --- |
| compareImage | object | O |  |  | - | Image to use for face validation |
| compareImage.url | string | △ |  |  | "https://..." | Image URL |
| compareImage.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| orientation | bool |  | true | true, false | false | Whether to use face orientation recognition function |
| mask | bool |  | true | true, false | false | Whether to use the mask wear recognition function |

* Must have only either compareImage.url or compareImage.bytes


<details>
<summary>Request example</summary>

```
$ curl -X POST '{domain}/nhn-face-reco/v1.0/appkeys/{appKey}/verify/groups/{group-id}/faces/{face-id}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "compareImage": {
        "url": "https://..."
    }
}'
```

</details>


#### Response

* [Response body header description omitted]
  * This information is available in [Common Response Information](./api-guide-v1.0/#common-response)

[Response body data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.similarity | float | O | 98.156 | Similarity value between 0 and 100 |
| data.face | object | O | - | A face registered using face registration API |
| data.face.bbox | object | O | - | Bounding box information of a face detected in the image |
| data.face.bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.face.bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.face.bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.face.bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.face.confidence | float | O | 99.9123 | Face recognition confidence |
| data.face.faceId | string | O | "9297db50-d4f2-c6b8-ea05-edf2013089fd" | Face ID |
| data.face.imageId | string | O | "87db50d4-f2c6-b8ea-05ed-9f201309fd92" | Image ID<br>There can be multiple face IDs per image ID |
| data.face.externalImageId | string |  | "image01.jpg" | Values registered in the image by user |
| data.sourceFace | object | O | - | The image delivered to Request Body |
| data.sourceFace.bbox | object | O | - | Bounding box information of a face detected in the image |
| data.sourceFace.bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.sourceFace.bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.sourceFace.bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.sourceFace.bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.sourceFace.landmarks | array | O | - | Facial characteristics |
| data.sourceFace.landmarks[].type | string | O | "leftEye" | List of valid values:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.sourceFace.landmarks[].y | float | O | 0.362 | y coordinate of the facial characteristic |
| data.sourceFace.landmarks[].x | float | O | 0.362 | x coordinate of the facial characteristic |
| data.sourceFace.orientation | object |  | 0.362 | Face angle |
| data.sourceFace.orientation.x | float |  | 15.303436 | Face left/right angle(Yaw) |
| data.sourceFace.orientation.y | float |  | -9.222179 | Face up/down angle(Pitch) |
| data.sourceFace.orientation.z | float |  | -7.97249 | Face angle compared to level surface(Roll) |
| data.sourceFace.mask | boolean |  | false | Whether to wear a mask |
| data.sourceFace.confidence | float | O | 99.9123 | Face recognition confidence |



<details>
<summary>Response body example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    },
    "data": {
        "similarity": 87.074165,
        "face": {
            "bbox": {
                "x0": 0.828,
                "y0": 0.07829181494661921,
                "x1": 0.886,
                "y1": 0.2117437722419929
            },
            "confidence": 0.998737,
            "faceId": "bd21a5d1-64bc-4723-a041-71d720fe56d8",
            "imageId": "165b63c9-9564-4a65-b3f6-7bb64d4fe9f9",
            "externalImageId": "user-defined-external-image-id"
        },
        "sourceFace": {
            "bbox": {
                "x0": 0.26785714285714285,
                "y0": 0.22767857142857142,
                "x1": 0.7366071428571429,
                "y1": 0.8660714285714286
            },
            "landmarks": [
                {
                    "type": "leftEye",
                    "x": 0.39285714285714285,
                    "y": 0.47767857142857145
                },
                {
                    "type": "rightEye",
                    "x": 0.6071428571428571,
                    "y": 0.4732142857142857
                },
                {
                    "type": "nose",
                    "x": 0.5,
                    "y": 0.6026785714285714
                },
                {
                    "type": "leftLip",
                    "x": 0.41964285714285715,
                    "y": 0.7276785714285714
                },
                {
                    "type": "rightLip",
                    "x": 0.5758928571428571,
                    "y": 0.7276785714285714
                }
            ],
            "orientation": {
                "x": 1.400425,
                "y": 6.624787,
                "z": -2.08028
            },
            "mask": false,
            "confidence": 0.999286
        }
    }
}
```

</details>


#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40030| NotFoundGroupError | Could not find the group ID |
|-40050| NotFoundFaceIDError | Could not find the face ID |
|-41000| UnauthorizedAppKey | Unauthorized Appkey |
|-45020| ImageTooLargeException | Image size exceeded |
|-45030| InvalidImageParameterException | Invalid image parameter. Mainly due to incorrect Base64 encoding |
|-45040| InvalidImageFormatException | Unsupported image format |
|-45050| InvalidImageURLException | Invalid image URL |
|-45060| ImageTimeoutError | Image download timeout |
|-50000| InternalServerError | Server error |
