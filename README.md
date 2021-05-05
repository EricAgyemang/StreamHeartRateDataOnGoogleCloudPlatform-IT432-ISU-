# INTRODUCTION
# IT432 Stream Heart Rate Data
This is an example of how to build a data pipeline that starts with an Internet of Things (IoT) device that captures the heart rate, leverage IoT core to securely publish the data to a message queue where it will then be transported into a data warehouse. Here, a Raspberry Pi with a heart rate sensor is used for the IoT device and components of Google cloud platform will form the data pipeline.

This example is not an officially supported Google product, does not have a SLA/SLO, and should not be used in production


# SECTION 1
# Supporting Hardware Targets
The following are currently supported hardware targets:
* Raspberry Pi 3 Model B with power supply 
* SD memorry card and case
* USB card reader
* Female-to-male breadboard wires
* Polar T34 Heart Rate Transmitter and Polar Heart Rate receiver
* A computer monitor or TV with HDMI input, HDMI cable, Keyboard and a mouse.
* A Google Cloud Platform account (New users are eligible for a $300 free trial)
* Gmail account


# SECTION 2
# Getting Set Up
  - Environment Setup
    * Sign-in to [Google Cloud Platform console](http://console.cloud.google.com/) 
    * You can use the default project ("My First Project") or create a new project by using [Manage resources page](https://console.cloud.google.com/cloud-resource-manager) eg: iot-heartrate. The Project ID must be unique name accross all Google Cloud projects
 
  - Enable APIs
    * This project uses the IoT Core, Pub/Sub, Dataflow, and Compute Engine, so enable the Cloud IoT API by opening the [Google Cloud IoT Core console](http://console.cloud.google.com/iot/).
    * Repeat this process for the Pub/Sub, Dataflow, and Compute Engine by clicking the "Enable API" tab.
    
    
 
    
