## AI Service > Face Recognition > API v2.0 Guide

* This document describes the APIs required for using Face Recognition API v2.0.
* SecretKey authentication is added starting with the Face Recognition API v2.0.

## API Common Information

### Preparations

- To use APIs, an integrated project Appkey or service Appkey is required.
    - We recommend using the integrated project Appkey.
    - You can create and use the integrated project Appkey from the API security settings in the project settings page.
    - You can check the Appkey and SecretKey in the **URL & Appkey** menu at the top of the console.

### Request Common Information

- The security key needs to be authenticated in order to use APIs.

[API domain]

| Domain |
| --- |
| https://face-recognition.api.nhncloudservice.com |

[Header]

| Name | Value | Description |
| --- | --- | --- |
| Authorization | {secretKey} | Security key issued from the console |

<span id="input-image-guide"></span>

### Input Image Guide

* Face images must be at least 80x80 px in width and height.
    * The face size must be at least 60*60 px to be eligible for facial recognition.
    * As the image gets bigger, the minimum face size must get bigger as well for better facial recognition precision.
    * The bigger the proportion of the face area in the image, the more precise the facial recognition.
* In the input image, both left/right angle(Yaw) and up/down angle(Pitch) of the face must be 45 degrees or less.
* Maximum size of image file: 3 MB (3,000,000 bytes)
* Supported image formats: PNG, JPEG
* If you specify a port directly in the image URL, only ports 80, 443, and 10000 to 12000 are available.

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

- This API creates groups. You can use [Register Face](./api-guide-v2.0/#add-face) to a created group to register faces.

#### Request

[URI]

| Method | URI |
| --- | --- |
| POST | /v2.0/appkeys/{appKey}/groups |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |

[Request Body]

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| groupId | string | O |  | [a-z0-9-]<br>Max. 255 characters | "my-group" | Group ID registered by user |

<details>
<summary>Request example</summary>

```
$ curl -X POST '{domain}/v2.0/appkeys/{appKey}/groups' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "groupId": "my-group"
}'
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

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
|-40070| ServiceQuotaExceededException | Exceeding the maximum number of groups you can create |
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
|-50000| InternalServerError | Server error |

### Group List

* This API views the list of groups.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /v2.0/appkeys/{appKey}/groups |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |

[URL Parameter]

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| limit | int | O |  | 1 ~ 200 | 100 | Max size |
| next-token | string |  |  |  | "skljsdioew..." | Value returned from 'Group list response body data'<br/> If the result is partially truncated, the next-token can be used to bring the rest of the result |

* Caution
    * In the beginning, the next-token cannot exist.
    * The token may disappear at a specific time or under specific conditions.
    * Upon issuing the token, the limit becomes fixed.

<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/v2.0/appkeys/{appKey}/groups?limit={limit}' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

* Requested using the next-token contained in the 'Group list response body data'

<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/v2.0/appkeys/{appKey}/groups?limit={limit}&next-token={next-token}' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

* If the next-token exists, the limit cannot be changed, and it is auto-set to the value from when the token was issued

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

[Response body data]

| Name | Type | Required | Examples | Description |
| --- | --- | --- | --- | --- |
| data.groupCount | int | O | 2 | Number of groups |
| data.groups[].groupId | string | O | "group-id" | Group ID registered by user |
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
    "groups": [
      {
        "groupId": "group-id"
      },
      {
        "groupId": "my-group"
      }
    ],
    "nextToken": "dlkj-210jwoivndslko9d..."
  }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40040| InvalidTokenError | Invalid token used |
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
|-50000| InternalServerError | Server error |

### Group Details

* This API views details of a specific group, such as group ID, model version, number of faces registered in the group, etc.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /v2.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]<br>Max. 255 characters |

<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

[Response body data]

| Name | Type | Required | Examples | Description |
| --- | --- | --- | --- | --- |
| data.groupId | string | O | "group-id" | Group ID registered by user |
| data.createTime | string | O | "2020-11-04T12:36:24" | Time when the group was created |
| data.faceCount | int |  | 365 | Number of faces registered in the group |

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
    "groupId": "group-id",
    "createTime": "2020-09-29T14:34:12",
    "faceCount": 365
  }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
|-40000| InvalidParam | The parameter contains an error |
|-40030| NotFoundGroupError | Could not find the group ID |
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
|-50000| InternalServerError | Server error |

### Delete Group

* This API permanently deletes a group and all face information of the group.

#### Request

[URI]

| Method | URI |
| --- | --- |
| DELETE | /v2.0/appkeys/{appKey}/groups/{group-id} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]<br>Max. 255 characters |

<details>
<summary>Request example</summary>

```
$ curl -X DELETE '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

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
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
|-50000| InternalServerError | Server error |

<span id="detect-face"></span>

### Recognize Face

* This API recognizes faces from input image.
* Returns the position data of the face, eyes, nose, and moth and the confidence value from the recognized face.
* Recognizes up to 20 faces from the input image in the order from the largest to smallest face.
* The input image can be delivered via Base64-encoded image bytes or image url.
* To find out more about input image, see [Input Image Guide](./api-guide-v2.0/#input-image-guide).

<span id="detect-face-request"></span>

#### Request

[URI]

| Method | URI |
| --- | --- |
| POST | /v2.0/appkeys/{appKey}/faces/detect |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |

[Request Body]

#### Content-Type: application/json

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| image | object | O |  |  | - | Image used for face detection |
| image.url | string | △ |  |  | "https://..." | Image URL |
| image.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| orientation | bool |  | true | true, false | false | Whether to use face orientation detection |
| mask | bool |  | true | true, false | false | Whether to use mask wear detection |

* Must have only one of either image.url or image.bytes.

#### Content-Type: multipart/form-data

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| imageFile | file | O |  | | image.png | Image file used for face detection | 
| orientation | bool |  | true | true, false | false | Whether to use face orientation detection |
| mask | bool |  | true | true, false | false | Whether to use mask wear detection |

<details>
<summary>Request example</summary>

```
$ curl -X POST '{domain}/v2.0/appkeys/{appKey}/faces/detect' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "image": {
        "url":"https://..."
    }
}'
```

```
$ curl -X POST -H 'Authorization: {secretKey}' -H 'Content-Type: multipart/form-data' -F imageFile=@image.png '{domain}/v2.0/appkeys/{appKey}/faces/detect'
```

</details>

<span id="detect-face-response"></span>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

[Response body data]

| Name | Type | Required | Examples | Description |
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
    "faceDetails": [
      {
        "bbox": {
          "x0": 0.36,
          "y0": 0.21,
          "x1": 0.612,
          "y1": 0.715
        },
        "landmarks": [
          {
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
          },
          {
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
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
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
* The saved vector data gets used as characteristic vectors for the [Search face by face ID](./api-guide-v2.0/#search-by-face-id)and [Search face by image](./api-guide-v2.0/#search-by-image) APIs.
* The input image can be delivered via Base64-encoded image bytes or image url.
* To find out more about input image, see [Input Image Guide](./api-guide-v2.0/#input-image-guide).
* 'imageId' is a value given for the input image, and the 'externalImageId' is a value which can be directly given by the user. The user can utilize 'imageId' and 'externalImageId' to perform labeling for the image or face ID from the user-end, and they can also be used on their own like indexes.
* 'imageId' and 'externalImageId' are returned from the response of the [Face list within a group](./api-guide-v2.0/#face-list-in-a-group) and [Search face by face ID](./api-guide-v2.0/#search-by-face-id) and [Search face by image](./api-guide-v2.0/#search-by-image) APIs.
* Up to 100,000 faces can be registered per single group.

<span id="add-face-request"></span>

#### Request

[URI]

| Method | URI |
| --- | --- |
| POST | /v2.0/appkeys/{appKey}/groups/{group-id}/faces |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]<br>Max. 255 characters |

[Request Body]

#### Content-Type: application/json

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| image | object | O |  |  | - | Image used for face registration |
| image.url | string | △ |  |  | "https://..." | Image URL |
| image.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| limit | int | O |  | 1~20 | 3 | Max number of faces to register in the group after sorting the faces recognized from the input image in order of largest to smallest |
| externalImageId | string |  |  | [a-zA-Z0-9_.-:]<br>Max. 255 characters | "image01.jsp" | Values provided by user to create a label for an image or face ID |
| orientation | bool |  | true | true, false | false | Whether to use face orientation detection |
| mask | bool |  | true | true, false | false | Whether to use mask wear detection |

* Must have only one of either image.url or image.bytes.

#### Content-Type: multipart/form-data

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| imageFile | file | O |  | | image.png | Image file used for face registration | 
| limit | int | O |  | 1~20 | 3 | Max number of faces to register in the group after sorting the faces recognized from the input image in order of largest to smallest |
| externalImageId | string |  |  | [a-zA-Z0-9_.-:]<br>Max. 255 characters | "image01.jsp" | Values provided by user to create a label for an image or face ID |
| orientation | bool |  | true | true, false | false | Whether to use face orientation detection |
| mask | bool |  | true | true, false | false | Whether to use mask wear detection |

<details>
<summary>Request example</summary>

```
$ curl -X POST '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}/faces' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "image": {
        "url": "https://..."
    },
    "externalImageId": "image01.jpg",
    "limit": 3
}'
```

```
$ curl -X POST -H 'Authorization: {secretKey}' -H 'Content-Type: multipart/form-data' -F imageFile=@image.png -F externalImageId=image01.jsp -F limit=3 '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}/faces'
```

</details>


<span id="add-face-response"></span>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

[Response body data]

| Name | Type | Required | Examples | Description |
| --- | --- | --- | --- | --- |
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
    "addedFaceCount": 1,
    "addedFaces": [
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
      }
    ],
    "addedFaceDetails": [
      {
        "bbox": {
          "x0": 0.36,
          "y0": 0.21,
          "x1": 0.612,
          "y1": 0.715
        },
        "landmarks": [
          {
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
          },
          {
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
      }
    ],
    "notAddedFaceCount": 1,
    "notAddedFaces": [
      {
        "bbox": {
          "x0": 0.36,
          "y0": 0.21,
          "x1": 0.612,
          "y1": 0.715
        },
        "landmarks": [
          {
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
          },
          {
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
      }
    ]
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
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
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
| DELETE | /v2.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]<br>Max. 255 characters |
| face-id | Registered face ID |

<details>
<summary>Request example</summary>

```
$ curl -X DELETE '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

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
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
|-50000| InternalServerError | Server error |

<span id="face-list-in-a-group"></span>

### List of Faces within Group

* This API views the face info list registered for a specific group.
* Returns the face info array in order of most recently registered.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /v2.0/appkeys/{appKey}/groups/{group-id}/faces |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]<br>Max. 255 characters |

[URL Parameter]

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| limit | int | O |  | 1 ~ 200 | 100 | Max size |
| next-token | string |  |  |  | "skljsdioew..." | Value returned from 'Group list response body data'<br/> If the result is partially truncated, the next-token can be used to bring the rest of the result |

* Caution
    * In the beginning, the next-token cannot exist.
    * The token may disappear at a specific time or under specific conditions.
    * Upon issuing the token, the limit becomes fixed.

<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}/faces?limit={limit}' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

* Requested using the next-token contained in the 'Group list response body data'

<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}/faces?limit={limit}&next-token={next-token}' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

* If the next-token exists, the limit cannot be changed, and it is auto-set to the value from when the token was issued

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

[Response body data]

| Name | Type | Required | Examples | Description |
| --- | --- | --- | --- | --- |
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
    "faceCount": 2,
    "faces": [
      {
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
      }
    ],
    "nextToken": "dlkj-210jwoivndslko9d..."
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
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
|-50000| InternalServerError | Server error |

<span id="search-by-face-id"></span>

### Search face by face ID

* This API searches for faces from a specific group using the face ID.
* Returns the array of the face info in order of the most to least similar.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /v2.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}/search |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]<br>Max. 255 characters |
| face-id | Face ID to compare |

[URL Parameter]

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| limit | int | O |  | 1~100 | 100 | Max size |
| threshold | int | O |  | 1~100 | 90 | A similarity reference value that determines a match |

<details>
<summary>Request example</summary>

```
$ curl -X GET '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}/search?limit={limit}&threshold={threshold}' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8'
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

[Response body data]

| Name | Type | Required | Examples | Description |
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
    "matchFaces": [
      {
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
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
|-50000| InternalServerError | Server error |

<span id="search-by-image"></span>

### Search face by image

* Uses the largest face recognized from the input image to compare if it matches a face from a specific group.
* The input image can be delivered via Base64-encoded image bytes or image url.
* To find out more about input image, see [Input Image Guide](./api-guide-v2.0/#input-image-guide).
* Returns the array of the face info in order of the most to least similar.

<span id="search-by-image-request"></span>

#### Request

[URI]

| Method | URI |
| --- | --- |
| POST | /v2.0/appkeys/{appKey}/groups/{group-id}/faces/search |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group id registered by user<br>[a-z0-9-]<br>Up to 255 characters |

[Request Body]

#### Content-Type: application/json

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| image | object | O |  |  | - | Image used for face search |
| image.url | string | △ |  |  | "https://..." | Image URL |
| image.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| limit | int | O |  | 1~100 | 100 | Max size |
| threshold | int | O |  | 1~100 | 90 | A similarity reference value that determines a match |
| orientation | bool |  | true | true, false | false | Whether to use face orientation detection |
| mask | bool |  | true | true, false | false | Whether to use mask wear detection |

* Must have only one of either image.url or image.bytes.

#### Content-Type: multipart/form-data

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| imageFile | file | O |  |  | image.png | Image file used for face search |
| limit | int | O |  | 1~100 | 100 | Max size |
| threshold | int | O |  | 1~100 | 90 | A similarity reference value that determines a match |
| orientation | bool |  | true | true, false | false | Whether to use face orientation detection |
| mask | bool |  | true | true, false | false | Whether to use mask wear detection |

<details>
<summary>Request example</summary>

```
$ curl -X POST '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}/faces/search' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "image": {
        "url": "https://..."
    },
    "limit": 100
    "threshold": 90
}'
```

```
$ curl -X POST -H 'Authorization: {secretKey}' -H 'Content-Type: multipart/form-data' -F imageFile=@image.png -F limit=100 threshold=90 '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}/faces/search'
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

[Response body data]

| Name | Type | Required | Examples | Description |
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
    "matchFaceCount": 2,
    "matchFaces": [
      {
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
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
|-45020| ImageTooLargeException | Image size exceeded |
|-45030| InvalidImageParameterException | Invalid image parameter. Mainly due to incorrect Base64 encoding |
|-45040| InvalidImageFormatException | Unsupported image format |
|-45050| InvalidImageURLException | Invalid image URL |
|-45060| ImageTimeoutError | Image download timeout |
|-50000| InternalServerError | Server error |

<span id="compare-face"></span>

### Compare Faces

* Compares the similarity of the faces recognized from the reference image(sourceImage) and comparison image(targetImage).
* Out of the faces recognized from the reference image, only the largest face(source face) is used.
* The input image can be delivered via Base64-encoded image bytes or image url.
* To find out more about input image, see [Input Image Guide](./api-guide-v2.0/#input-image-guide).
* Returns the array of the face info in order of the most to least similar.

<span id="compare-face-request"></span>

#### Request

[URI]

| Method | URI |
| --- | --- |
| POST | /v2.0/appkeys/{appKey}/faces/compare |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |

[Request Body]

#### Content-Type: application/json

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| sourceImage | object | O |  |  | - | Reference image for facial comparison<br/>(=referenceImage) |
| sourceImage.url | string | △ |  |  | "https://..." | Image URL |
| sourceImage.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| targetImage | object | O |  |  | - | Image containing the target face for comparison<br/>(=comparisonImage) |
| targetImage.url | string | △ |  |  | "https://..." | Image URL |
| targetImage.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| threshold | int | O |  | 1~100 | 90 | A similarity reference value that determines a match |
| orientation | bool |  | true | true, false | false | Whether to use face orientation detection |
| mask | bool |  | true | true, false | false | Whether to use mask wear detection |

* Must have only either sourceImage.url or sourceImage.bytes.
* Must have only either targetImage.url or targetImage.bytes.

#### Content-Type: multipart/form-data

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| sourceImageFile | object | O |  |  | sourceImage.png | Reference image file for facial comparison<br/>(=referenceImage) |
| targetImageFile | object | O |  |  | targetImage.png | Image file containing the target face for comparison<br/>(=comparisonImage) |
| threshold | int | O |  | 1~100 | 90 | A similarity reference value that determines a match |
| orientation | bool |  | true | true, false | false | Whether to use face orientation detection |
| mask | bool |  | true | true, false | false | Whether to use mask wear detection |

<details>
<summary>Request example</summary>

```
$ curl -X POST '{domain}/v2.0/appkeys/{appKey}/faces/compare' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "sourceImage": {
        "url": "https://..."
    },
    "targetImage": {
        "url": "https://..."
    }
}'
```

```
$ curl -X POST -H 'Authorization: {secretKey}' -H 'Content-Type: multipart/form-data' -F sourceImage=@sourceImage.png -F targetImage=@targetImage.png -F threshold=90 '{domain}/v2.0/appkeys/{appKey}/faces/compare'
```

</details>

<span id="compare-face-response"></span>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

[Response body data]

| Name | Type | Required | Examples | Description |
| --- | --- | --- | --- | --- |
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
    "matchedFaceDetailCount": 2,
    "matchedFaceDetails": [
      {
        "faceDetail": {
          "bbox": {
            "x0": 0.36,
            "y0": 0.21,
            "x1": 0.612,
            "y1": 0.715
          },
          "landmarks": [
            {
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
            },
            {
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
      },
      {
        "faceDetail": {
          "bbox": {
            "x0": 0.36,
            "y0": 0.21,
            "x1": 0.612,
            "y1": 0.715
          },
          "landmarks": [
            {
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
            },
            {
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
      }
    ],
    "unmatchedFaceDetailCount": 1,
    "unmatchedFaceDetails": [
      {
        "faceDetail": {
          "bbox": {
            "x0": 0.36,
            "y0": 0.21,
            "x1": 0.612,
            "y1": 0.715
          },
          "landmarks": [
            {
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
            },
            {
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
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
|-45020| ImageImageTooLargeException:{Source/Target} | {Source/Target} Image: Image size exceeded |
|-45030| InvalidImageParameterException:{Source/Target} | Invalid image parameter. Mainly due to incorrect Base64 encoding |
|-45040| ImageInvalidImageFormatException:{Source/Target} | {Source/Target} image: Unsupported image format |
|-45050| ImageInvalidImageURLException:{Source/Target} | {Source/Target} image: Invalid image URL |
|-45060| ImageImageTimeoutError:{Source/Target} | {Source/Target} Image: Image download timeout |
|-50000| InternalServerError | Server error |

<span id="verify"></span>

### Face Verification

* This function compares the face ID of a specific face registered in advance with the face detected in the input image and returns a similarity value.
* Use [Register Face](./api-guide-v2.0/#add-face) to a created group to register faces.
* Only the largest face detected in the input image is used.
* The input image can be delivered via Base64-encoded image bytes or image url.
* To find out more about input image, see [Input Image Guide](./api-guide-v2.0/#input-image-guide).

<span id="verify-request"></span>

#### Request

[URI]

| Method | URI |
| --- | --- |
| POST | /v2.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}/verify |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| group-id | Group ID registered by user<br>[a-z0-9-]<br>Max. 255 characters |
| face-id | Registered face ID |

[Request Body]

#### Content-Type: application/json

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| compareImage | object | O |  |  | - | Image used for face verification |
| compareImage.url | string | △ |  |  | "https://..." | Image URL |
| compareImage.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| orientation | bool |  | true | true, false | false | Whether to use face orientation detection |
| mask | bool |  | true | true, false | false | Whether to use mask wear detection |

* Must have only either compareImage.url or compareImage.bytes

#### Content-Type: multipart/form-data

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| imageFile | file | O |  |  | image.png | Image file used for face verification |
| orientation | bool |  | true | true, false | false | Whether to use face orientation detection |
| mask | bool |  | true | true, false | false | Whether to use mask wear detection |

<details>
<summary>Request example</summary>

```
$ curl -X POST '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}/verify' -H 'Authorization: {secretKey}' -H 'Content-Type: application/json;charset=UTF-8' -d '{
    "compareImage": {
        "url": "https://..."
    }
}'
```

```
$ curl -X POST -H 'Authorization: {secretKey}' -H 'Content-Type: multipart/form-data' -F imageFile=@image.png '{domain}/v2.0/appkeys/{appKey}/groups/{group-id}/faces/{face-id}/verify'
```

</details>

#### Response

* [Response body header description omitted]
    * This information is available in [Common Response Information](./api-guide-v2.0/#common-response)

[Response body data]

| Name | Type | Required | Examples | Description |
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
    "similarity": 29.507784,
    "face": {
      "bbox": {
        "x0": 0.15520833333333334,
        "y0": 0.2222222222222222,
        "x1": 0.24479166666666666,
        "y1": 0.45185185185185184
      },
      "confidence": 0.997507,
      "faceId": "b460baac-190d-448e-9f29-c2d5d429f388",
      "imageId": "f43a7bee-6a33-4450-82dd-16adfc6788ef",
      "externalImageId": "imsa-control-wb801-test"
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
|-41005| UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
|-45020| ImageTooLargeException | Image size exceeded |
|-45030| InvalidImageParameterException | Invalid image parameter. Mainly due to incorrect Base64 encoding |
|-45040| InvalidImageFormatException | Unsupported image format |
|-45050| InvalidImageURLException | Invalid image URL |
|-45060| ImageTimeoutError | Image download timeout |
|-50000| InternalServerError | Server error |
