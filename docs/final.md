
# Engagement Interface Specification

## Perceptus AI

This document describes the interface to be used to communicate between Intela's Perceptus AI and the SnapITHD Engagement application. The interface is in two parts. The first part is the interface created by Intela to allow Engagement to request a video asset to be analysed. The second part is the interface provided by SnapITHD to allow Perceptus to communicate anomalies in the form of 'AI events'. The diagram below provides an overview of the elements included in this interface specification. Refer to the interface details for more information.

![PerceptusInterfaceOverview](PerceptusInterface.png)

*Overview of interface*

## PART 1 - Perceptus Request

----
  The Perceptus request is the trigger to analyse a video file and perform anomaly detection on that file.

* **URL**

  *---To be decided---*

* **Method:**
  
  `POST`
  
* **URL Params**

   None

* **Data Params**

     **Required:**

   ```json
      {
        vessel_id : [integer], //Vessel identifier associated with video asset
        start_timestamp : [integer], //UNIX UTC timestamp of the video asset start
        secure_url : [alphanumeric], //URL to access the video asset
        frame_rate : [integer] //Frames per second for the video asset
      }
    ```

* **Success Response:**
  
  * **Code:** `200`
  
    **Content:** `Success`

* **Error Response:**

  * **Code:** `500`
  
    **Content:**

    ```json
      { error : "<Error Message>" }
    ```

* **Sample Call:**

  ```json
    {
        "vessel_id" : 1,
        "start_timestamp" : 1555454453,
        "secure_url": "https://negf.s3.amazonaws.com/secure/videos/atlas/6/2018/08/17/20180817-060827.mp4?AWSAccessKeyId=AKIAIR3WOZWOAGJ75JMA&Signature=ELhM76Vg6uWWP%2ByHYEDoZReQTK0%3D&Expires=1551463737",
        "frame_rate": 15,
    }
  ```

* **Notes:**

  POST parameters are expected in JSON format.
  Secure URLs are created by Engagement using Amazon Web Services. The link will expire after the timestamp value in the `Expires` parameter in the secure URL.

## PART 2 - Engagement Request

----
  The Engagement request will be called by Perceptus upon completion of anomaly detection. A Vessel identifier and a list of timestamps (start and end in UTC) will be converted into a list of 'AI Events' to be displayed in Engagement Reviews.

* **URL**

  /api/v2/shdevent/aieventlist

* **Method:**
  
  `POST`
  
* **URL Params**

   None

* **Data Params**

    **Required:**

    ```json
      {
        vessel_id : [integer], //Vessel identifier supplied to Perceptus
        events : [
          [integer], //UNIX UTC timestamp of event start
          [integer] //UNIX UTC timestamp of event end
        ]
      }
    ```

* **Success Response:**
  
  * **Code:** `200`
  
    **Content:** `Success`

* **Error Response:**

  * **Code:** `500`
  
    **Content:**

    ```json
      { error : "<Error Message>" }
    ```

  * **Code:** `404`

    **Content:**

    ```json
      { error : "Vessel <vessel_id> not found" }
    ```

* **Sample Call:**

  ```json
    {
      "vessel_id" : 42,
      "events" : [
        [
          "1555459898",
          "1555459900"
        ],
        [
          "1555495401",
          "1555495953"
        ],
        [
          "1555460046",
          "1555787432"
        ]
      ]
    }
  ```

* **Notes:**

  POST parameters are expected in JSON format.
  The Vessel identifier supplied in the original Perceptus request should be returned in the associated Engagement request.
