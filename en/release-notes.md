<!-- pre-align:aligned sig=d9440986b5bc -->

<a id="ai-service-face-recognition-release-notes"></a>
## AI Service > Face Recognition > Release Notes { #ai-service-face-recognition-release-notes }

<a id="march-10-2026"></a>
### March 10, 2026 { #march-10-2026 }

<a id="march-10-2026-added-features"></a>
#### Added Features

* Release of API v2.1
    * Added API using User Access Key token authentication.
    * For more information on issuing and using User Access Key tokens, please refer to the [User Access Key Token](/nhncloud/en/public-api/user-access-key-token).

<a id="march-11-2025"></a>
### March 11, 2025 { #march-11-2025 }

<a id="march-11-2025-feature-updates"></a>
#### Feature Updates

* Made face detection 5-10% faster than before

<a id="september-10-2024"></a>
### September 10, 2024 { #september-10-2024 }

<a id="september-10-2024-added-features"></a>
#### Added Features

* [API v2.0] Added the feature to filter the list of faces by using face ID, Image ID, images or face ID labeling values (ExternalImageID) users set while registering faces, when retrieving [Face List In Group](./api-guide-v2.0/#face-list-in-a-group).

<a id="january-09-2024"></a>
### January 09, 2024 { #january-09-2024 }

<a id="january-09-2024-updates"></a>
#### Updates

* Error message update(`InvalidImageParameterException` -> `InvalidImageBytesException`)

<a id="october-31-2023"></a>
### October 31, 2023 { #october-31-2023 }

<a id="october-31-2023-added-features"></a>
#### Added Features

* API v2.0 released

<a id="december-27-2022"></a>
### December 27, 2022 { #december-27-2022 }

<a id="december-27-2022-added-features"></a>
#### Added Features

* [API] Added whether to wear a mask to FaceDetails in response body.
    * [Response of Face Recognize](./api-guide-v1.0/#detect-face-response)
    * [Response of Face Register](./api-guide-v1.0/#add-face-response)
    * [Response of Face Compare](./api-guide-v1.0/#compare-face-response)

<a id="march-29-2022"></a>
### March 29, 2022 { #march-29-2022 }

<a id="march-29-2022-feature-updates"></a>
#### Feature Updates

* Applied a new model with enhancements including the improved face recognition rate
    * The new model will be automatically applied to newly created groups from March 29, 2022
    * Groups that have already been created will use the existing model as it is.
    * To use the new model, you need to create a new group and register faces again.

<a id="july-27-2021"></a>
### July 27, 2021 { #july-27-2021 }

<a id="july-27-2021-added-features"></a>
#### Added Features

* [API] Added [Face Verification](./api-guide-v1.0/#verify) API

<a id="april-27-2021"></a>
### April 27, 2021 { #april-27-2021 }

<a id="april-27-2021-feature-updates"></a>
#### Feature Updates

* [API] Diversified input image formats

<a id="march-23-2021"></a>
### March 23, 2021 { #march-23-2021 }

* Released the Face Recognition Service
