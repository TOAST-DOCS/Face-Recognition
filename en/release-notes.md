## AI Service > Face Recognition > Release Notes

### March 11, 2025

#### Feature Updates

* Made face detection 5-10% faster than before

### September 10, 2024

#### Added Features

* [API v2.0] Added the feature to filter the list of faces by using face ID, Image ID, images or face ID labeling values (ExternalImageID) users set while registering faces, when retrieving [Face List In Group](./api-guide-v2.0/#face-list-in-a-group).

### January 09, 2024

#### Updates

* Error message update(`InvalidImageParameterException` -> `InvalidImageBytesException`)

### October 31, 2023

#### Added Features

* API v2.0 released

### December 27, 2022

#### Added Features

* [API] Added whether to wear a mask to FaceDetails in response body.
    * [Response of Face Recognize](./api-guide-v1.0/#detect-face-response)
    * [Response of Face Register](./api-guide-v1.0/#add-face-response)
    * [Response of Face Compare](./api-guide-v1.0/#compare-face-response)

### March 29, 2022

#### Feature Updates

* Applied a new model with enhancements including the improved face recognition rate
    * The new model will be automatically applied to newly created groups from March 29, 2022
    * Groups that have already been created will use the existing model as it is.
    * To use the new model, you need to create a new group and register faces again.

### July 27, 2021

#### Added Features

* [API] Added [Face Verification](./api-guide-v1.0/#verify) API

### April 27, 2021

#### Feature Updates

* [API] Diversified input image formats

### March 23, 2021

* Released the Face Recognition Service
