## AI Service > Face Recognition > Overview

* NHN Cloud's face recognition service has been developed by learning the quality facial dataset using the machine learning technology.
* It offers a variety of features including facial recognition, analysis, and comparison, and supports the identity verification feature in various scenarios.
* It can provide elaborate and personalized custom services in a variety of online and offline environments (e.g. financial, medical, and commercial industries) where facial recognition is applicable.

## Key Features

Provides the following features:

* **Face Detection & Analysis**
    * This feature lets you recognize faces from input image.
    * It analyzes the characteristics of the recognized faces and returns the position data of the face, eyes, nose, and mouth and the confidence value.
* **Face Comparison**
    * This feature lets you compare face recognized from input image with face recognized from the target image.
    * If the input image has multiple faces in it, the largest face will be compared with the faces recognized from the target image and returns the similarity value of each compared face.
* **Managing Group**
    * This feature lets you create a group to register faces and view or delete any group list created.
    * You can register the face recognized from the input image or view/delete the faces registered to the group.
* **Face Indexing & Searching**
    * This feature lets you pre-analyze and store facial data, and use a face ID or an image to search and find those faces from the provided images.
    * The analyzed facial data is managed with face IDs.
    * It returns a series of faces that match the input value (face ID or image) and sorts them by similarity (highest to lowest).
* **Face Verification**
    * This function compares the face ID of a specific face registered in advance with the face detected in the input image and returns a similarity value.
    * The user can set the reference value of similarity to determine whether the face is the same.
    * Raise the reference value of similarity to perform strict verification for a high level of security.
* **Face Spoofing Detection**
    * A detection feature to prevent illegal access to face recognition or spoofing (distortion in face recognition, such as face photos and videos).
    * Analyzes the detected face images and returns whether the face is spoofed or not.
    * The face spoofing detection feature is available in the Face Recognition API (Face Detection, Face Registration, Face Comparison, Face Search by Image, and Face Verification).

## Service targets

* For building an access control system utilizing the facial recognition technology
* For monitoring visitors
* For monitoring the position of the workers working in a construction site to ensure workplace safety
* For building a transaction system utilizing the facial recognition technology
* For searching photos containing a specific face from photo albums
* For determining whether the face image is spoofed or not

## Privacy Policy

- While using the face recognition service, the customer may collect personal and sensitive information of their users. Therefore, the customer of this service must inform a legal notice to their users as per the Personal Information Protection Act and acquire their consent regarding the matter.
Also during this process, work consignment relation regarding the processing of personal information may arise between the customer and NHN Cloud. The customer who assumes the position of consignor may enter into a consignment contract with the consignee, NHN Cloud, separately in writing, and post a privacy policy notice by referencing the following:

     \- Consignee: NHN Cloud
     \- Consignment Description: Providing face recognition service
 
 
