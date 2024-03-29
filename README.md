# INTRODUCTION
# Using [IoT Core to Stream Heart Rate Data](https://codelabs.developers.google.com/codelabs/iotcore-heartrate#0)
This is an example of how to build a data pipeline that starts with an Internet of Things (IoT) device that captures the heart rate, leverages IoT core to securely publish the data to a message queue where it will then be transported into a data warehouse. Here, a Raspberry Pi with a heart rate sensor is used for the IoT device, and components of the Google Cloud platform will form the data pipeline.

This example is not an officially supported Google product, does not have an **SLA/SLO**, and should not be used in production


# SECTION 1
# Supporting Hardware Targets
The following are currently supported hardware targets:
* Raspberry Pi 3 Model B with power supply. 
* SD memory card and case.
* USB card reader.
* Female-to-male breadboard wires.
* Polar T34 Heart Rate Transmitter and Polar Heart Rate receiver.
* A computer monitor or TV with HDMI input, HDMI cable, Keyboard, and a mouse.
* A Google Cloud Platform account (New users are eligible for a $300 free trial).
* Gmail account.

-------------
*Note:*

      1. The Raspberry Pi Zero W is recommended for this codelab but this project uses the Raspberry Pi 3 Model B which works.
      2. The Raspberry Pi Zero W require the programmer to have GPIO Hammer Headers or USB hub(to allow for connecting a keyboard and mouse into the USB port ib the Raspberry Pi), and soldering iron with solder.



# SECTION 2
# Getting Set Up
  - Environment Setup
    * Sign-in to [Google Cloud Platform console](http://console.cloud.google.com/) 
    * You can use the default project ("My First Project") or create a new project by using [Manage resources page](https://console.cloud.google.com/cloud-resource-manager). This project uses **iot-heartrate** as the project ID. 
    * The Project ID must be unique name accross all Google Cloud projects.
    * Go to [GitBash](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) or [OpenSSL](http://lunar.lyris.com/help/lm_help//11.3/Content/generating_public_and_private_keys.html) or [Google Cloud shell](https://www.cyberithub.com/5-best-steps-to-register-ssh-keys-in-google-cloudgcp/), follow the instructions given to create the pair of keys(private key and public key).
 
  - Enable APIs
    * This project uses the **IoT Core, Pub/Sub, Dataflow, and Compute Engine**, so enable the Cloud IoT API by opening the [Google Cloud IoT Core console](http://console.cloud.google.com/iot/).
    * Repeat this process for the **Pub/Sub, Dataflow, and Compute Engine** by clicking the **Enable API** tab.
    
     
# SECTION 3
# Create a BigQuery Table
* BigQuery is a serverless, highly scalable, low cost enterprise data warehouse to store data being stream from IoT devices.
* Create a table that will hold all of the IoT heart rate data.
    * From the **cloud console** select **BigQuery** > Click on the down arrow icon next to project name > select **Create new dataset** > **Enter heartRateData** > click **Ok**.
    * Click **"+"** sign next to your Dateset to create a new table
    * From **Source Data**, select **Create empty table**. 
    * For Destination table name, enter heartRateDataTable.
    * Under **Schema**, click the **Add Field** botton until there are a total of 4 fields. Fill in the fields by making sure to also select the appropriate **Type** for each field. 
    * Next, Click on the **Create Table** button.

----------------------------
*Note:* 
 
      1. Any spelling errors for the field names or the table name can cause issues when Cloud Functions attempts to add data later in this codelab.
      2. If you did make a mistake in creating the table, hover over the table name of the left side if the window, click the down arrow icon > click on Delete Table. 
      3. Repeate the steps in SECTION 3 above to create the table.
    
    
 # SECTION 4
 # Create a Pub/Sub Topic and Subscription
   Cloud Pub/Sub is a simple, reliable, scalable foundation for streaming data and event-driven computing systems. This will be used to pull IoT messages from.
   * From the [Google Cloud Console](https://console.cloud.google.com), select **Pub/Sub** > **Topics** > **Create a topic** > enter **"heartratedata"** as a topic name > click **Create**.
   * To allow other google cloud services to access those messages, we create a subscription. Clicking on the three dot menu to the right of the topic that was created > click on the **" :heavy_plus_sign: New subscription"** > enter **"heartratedata"** as subscription name > pull > click on **Create**.
 
    
# SECTION 4 
# Dataflow Template
A Dataflow template is used to create a process that monitors a Pub/Sub topic for incoming messages and then move messages to the BigQuery.
To create a Dataflow: 
* from the [Google Cloud Console](https://console.cloud.google.com), select Storage > **Browser.** > click **Bucket** button > **Create bucket**.
* Choose a name for the storage bucket (must be globally unique accross all Google Cloud).
* Select **Reginal** and make sure it is the sane region as the rest of your project's services > choose Location.
* Click **Create** button.
* Select **Dataflow > Create Job from Template > enter a job name** > select **Pub/Sub Subscription to BigQuery template**
* The **reginal endpoint** must match where the rest of the project resources are located. 
* Fill in the remainder of the field and make sure they are align with the name of your **storage bucket, Pub/Sub subscription**, and **BigQuery** dataset and **tablename**.
* Click on **Run job**

    
# SECTION 5
# Create an IOT Core registry
The **cloud IoT Core** is a fully managed service that allows the user to connect, manage, and ingest data from millions of global dispersed device. 
From the [Cloud Console](https://console.cloud.google.com),
* Select **Cloud IoT Core** > click on **device registry** > enter "heartrate" as the **Rsgistry ID**.
* Select a **Region** close to you > disable HTTP protocol > select Pub/Sub topic that was created in Section 4 above.
* Check the **MQTT** in the **Protocol** section. 
* Click on **Create** button.
    
# Assemble Raspberry Pi and Heart Rate Reciever
  * If there are more than three pins, trim the header down to only three pins.
  * Solder the header pins to the **sensor board**.
  * Insall the **hammer header pins** into the Raspberry Pi.
  * Format the **SD card** and install the **New Out Of Box Softwafare** (NOOBS) installer following the steps [here](https://www.raspberrypi.org/documentation/installation/noobs.md).
  * Insert the **SD card** into the Raspberry Pi(I recommend SD card with 32 GB).
  * Place the Raspberry Pi into its case.
  * Use the **breadboard wires** to connect the heart rate receiver to the Raspberry Pi.
 
 
  Raspberry Pi pin  | Receiver Connection 
  ------------------|--------------------
  Pin 16 (GPIO23)   | Not labelled
  Pin 17 (3V3)      | Not labelled
  Pin 20 (GND)      | GND
                    
  * Connect the monitor using the **mini-HDMI connector**, keyboard and a mouse with the **USB hub**.
  * Connect the Raspberry Pi to the **power adapter**.

# Configure the Raspberry Pi
We configure the Raspberry Pi by going through the following steps.
  * Booth the Raspberry Pi.
  * Select **Raspbian** for the desired operating system.
  * Make certain your desired language is correct and click on install (Hard drive icon on the upper left portion of the window).
  * Click on the **wifi icon** at the top right of the screen.
  * Select a **network**. If it is a secured network, enter the password(pre-shared key).
  * Click on the **raspberry icon** on the top left of the screen.
  * Select **Preferences** and the **Raspberry Pi Configuration**.
  * From the interface tab, enable **I2C** and **SSH**.
  * From the Localisation tab, set the **Locale** and **Timezone**. 
  * Allow Pi to reboot.
  * Click on the **Terminal icon** to open a terminal window.

# Install The Software
* All software on the Raspberry Pi must be up to date and needed packages are installed.
------------------------------

    sudo apt-get update
    sudo apt-get install git
    
* Clone the project code for the heart rate receiver
-------------------------------

    git clone https://github.com/googlecodelabs/iotcore-heartrate
    cd iotcore-heartrate
    
* Ensure the the required core packages are all installed.
------------------------------

    chmod +x initialsoftware.sh./initialsoftware.sh
    
# Create a security certificate
* In order to communicate with Google Cloud, a security certificate must be generated and then registered with IoT core.
-------------------------------

      chmod +x generate_keys.sh./generate_keys.sh
    
* In order to transfer the created **key** to your computer so that it can later be registered with IoT Core,
  use the **Secure File Transfer Protocol** (SFTP). 
* Go to the guid [here](https://www.codedonut.com/raspberry-pi/set-ssh-sftp-raspberry-pi/) to walk through the specifics
  of using **SFTP** on various operating systems.
* In order to connect via **SFTP**, the **IP address** of the Raspberry Pi must be known. Open the **terminal window** on the 
  Raspberry Pi and type **"ifconfig"** to get the IP address required. The IP address will be listed in the wlan0 block
  of the information after the word **"inet"**.
* Once you are connected with **SFTP**, you need to navigate to the directory where the security **certificate/keys** are 
  located (**/home/pi/.ssh**) and will want to copy the key **(ec_public.pub)**
  
# SECTION 6
# Add Device to the IoT Core Registry
From the [Cloud Console](https://console.cloud.google.com), return to IoT core.
* Click the **heartrate registry**.
* Click **Add device**.
* For the **Device ID**, enter raspberryHeartRate.
* For **public key format**, select **ES256**.
* For **authentication**, either choose **manual** and then copy/paste the value from the key file into
  the public key value area or choose **upload** and then upload the key from your computer.
* Click **Add**.

The IoT Core is now ready to receive communication from the Raspberry Pi.

# SECTION 7
# Start the Data.
# Data Streaming from Raspberry Pi
* Go to [Putty](https://www.youtube.com/watch?v=uNStEDWnPxY), follow the instructions to Connect the setput to via Putty  
* Put on the heart rate strap and the start the script that will receive the heartbeat signals.
* Calculate heart rate in beats per minutes and push the data to Google Cloud.
* if you are not in the **/home/pi/iotcore-heartrate** directly on the Raspberry Pi, move there first.
-------------------------------
    
    cd /home/pi/iotcore-heartrate
    
* Start the heart rate script by changing the **myproject, myregistry**, and *** mydevice** to match your 
  project's **project_id, registry_id, and device_id. 
-------------------------------
    
    python checkHeartRate.py --project_id=myproject --registry_id=myregistry --device_id=mydevice
* You should see the terminal window echo the heart rate data results about every 10 seconds (Check that Data is Flowing).
* You now have a completed IoT heart rate sensor that is streaming data to Google Cloud.

-------------------------------
 *Note:* 
 
    1. If you would like to move around and collect data, it is possible to use a USB Battery Pack to power the Raspberry Pi for several hours. .1
    2. If you will be going beyond WiFi range, using your cellphone as a WiFi hotspot and configuring the Raspberry Pi to connect to it can allow you to travel  anywhere that your cellular signal allows while continuing to stream heart rate data to the Google Cloud.
    3. If you receive an error message, the most common mistake is to omit the "–" in the commands above.

# SECTION 8
# BigQuery Data
Check to make sure that the data is flowing into the **BigQuery** table.
* From the [Cloud Console](https://console.cloud.google.com), go to [BigQuery](bigquery.cloud.google.com)
* Under **Project name** click on the **Dataset(HeartRateData)**.
* On the **table(heartRateDataTable)** click on **Query Table** button.
* Add an asterisk to the SQL statement so it reads *SELECT * FROM iot-heartrateit432.heartRateData.heartRateDataTable LIMIT 1000*
* Then add "ORDER BY timecollected ASC"
* Click the **RUN QUERY** button

If you see results, then the data is flowing properly and you are now ready to visualize the data.

# SECTION 9
# Data Visualization Using Google Sheets
* From the **Query results**, click on **save to Google Sheets**.
* When the results have been saved, click on the link that says **Click to View**
* Highlight the two columns that contain the **timecollected** and **heartrate**.
* Select **Insert** and **Chart** from the top menu
* If the chart appears as a histogram, use the **Chart Editor** on the right side of the screen 
  to select the drop-down options for Chart type.
* Select a **line graph** (top left option).
 
# SUMMARY
This project builds up a data pipeline that starts with an Internet of Things (IoT) device that captures the heart rate collected from a
patient and leverages the IoT core to securely publish the data to a message queue where it is transported into a data warehouse. In this project,
a Raspberry Pi with a heart rate sensor is used for the IoT device and the components of the Google Cloud platform form the data pipeline. 
Section 1 outlines the supporting hardware targets and hardware requirements, while Section 2 gives the details of getting the setup 
established on the Google Cloud platform. The key areas of section 2 are the creation and use of project ID and GitBush/OpenSSL/Google 
Cloud Shell for creating the two pair of keys (private key and public key) to be used for adding devices to the IoT core registry.
Section 3 outlines the steps required for creating BigQuery Table on the Google Cloud. Section 4 to Section 8 show the remaining steps 
and processes required to connect the Raspberry Pi for performing data streaming on the Google Cloud platform. The software requirements are
also given in this section.
The last Section 9 is about data visualization where detailed instructions are given to go through that process. In this, the 
results of the data streaming are saved to Google Sheets for visualization. Some helpful links with resources are provided and all supporting 
documents to this project are made available on GitHub for user perusal. 
   
 
# For More Information
* [Using IoT Core gateways with a Raspberry Pi](https://cloud.google.com/community/tutorials/cloud-iot-gateways-rpi)
* [Rapid Hardware Prototyping: Connect Your Raspberry Pi to Google Cloud IoT](https://lembergsolutions.com/blog/rapid-hardware-prototyping-connect-your-raspberry-pi-google-cloud-iot)
* [Tutorial on Google IoT Core with Raspberry Pi to visualize the sensor data](https://iot4beginners.com/tutorial-on-google-iot-core-with-raspberry-pi-to-visualize-the-sensor-data/)
* [How to create and connect to Google Cloud Virtual Machine with SSH](https://www.freecodecamp.org/news/how-to-create-and-connect-to-google-cloud-virtual-machine-with-ssh-81a68b8f74dd/)
* [Google Cloud IoT Core with Raspberry Pi](https://www.youtube.com/watch?v=3Zwlj9x96Jg)
* [Cloud IoT step-by-step: Connecting Raspberry PI + Python](https://medium.com/google-cloud/cloud-iot-step-by-step-connecting-raspberry-pi-python-2f27a2893ab5)
* [How to Make A Proper Connect between Raspberry pi and gcloud-IoT](https://www.dckap.com/blog/how-to-make-a-proper-connect-between-raspberry-pi-and-gcloud-iot/)
* [The Raspberry Pi: Everything You Need to Know to Get Started](https://www.makeuseof.com/tag/raspberry-pi-faq/)
* [Raspberry Pi FAQ](https://www.raspberrypi.org/documentation/faqs/)

# DISCLAIMER
This project is not an official Google project. It is not supported by Google and Google specifically disclaims
all warranties as to its quality, merchantability, or fitness for a particular purpose.









