---
title: Twitter JSON
description: A Tutorial for Parsing JSON Data on the Feather
authors:  Mark Olson
updated:  2018-11-14
---

## About this Tutorial

Some of your projects require querying the API of an online service (such as Instagram) and then parsing the data sent back from the service.   [JSON](https://www.json.org/) (JavaScript Object Notation) is a common data format returned by API queries.  In this tutorial you will explore how to request JSON data from an API using an HTTP GET request and then how to parse that data for use by your Feather.  

Specifically, this tutorial outlines how to query the Twitter API and its ["Tweet JSON"](https://developer.twitter.com/en/docs/tweets/data-dictionary/overview/intro-to-tweet-json.html) data.  

## Install ArduinoJSON Library

First, install Benoit Blanchon's [ArduinoJson library](https://arduinojson.org/).   Proceed with the steps below and/or consult arduinojson.org's [official documentation](https://arduinojson.org/v5/doc/installation/).  

1.  Launch the Arduino application.

1. From the main menu, choose Sketch --> Include Library --> Manage Libraries

1. In the "Filter your search..." box, type `JSON`

1. Scroll down until you find "ArduinoJson by Benoit Blanchon"

    !!! important

        Do NOT immediately click Install.  The Library Manager lists the "beta" version by default, but for reliability, you will most likely want to use the latest "production" version.  As of the  of the writing of this tutorial, the latest version is:
        
        **`version 5.13.3`**

1. From the "Version" drop-down box, scroll down past all of the "X.X-beta" versions until you find the latest version non-beta version.  Click on that version to select it.

1. Finally, click the `Install` button and then `Close` once the library installation has completed.

## Standard WiFi Connection

By now you should be familiar with this section, which outlines the Arduino code necessary to connect the Feather M0 to a WiFi access point.

1. Create a new Arduino sketch by choosing File --> New from the main menu.

1. Save it by choosing File --> Save and give it a memorable name, such as `TwitterDemo`

1.  Replace the code in the Sketch with the code below:

    ```arduino
    // Arduino library dependencies
    #include <SPI.h>
    #include <WiFi101.h>

    // ************
    // Enter your wifi access credentials in the tab: arduino_secrets.h
    // ************
    #include "arduino_secrets.h"
    char ssid[] = SECRET_SSID;
    char pass[] = SECRET_PASS;
    int wifi_status = WL_IDLE_STATUS;     // the WiFi radio's status

    // instantiate a secure  WiFi client
    // (most APIs require https connections)
    WiFiSSLClient WiFiclient;

    void setup(){

        //Initialize serial:
        Serial.begin(9600);

        // Feather M0-specific WiFi pins
        WiFi.setPins(8, 7, 4, 2);

        // do initial WiFi connect
        wifiConnect();

    }

    void loop(){

        // check if wifi lost and if yes, reconnect
        if (WiFi.status() != WL_CONNECTED){
            Serial.println("WiFi connection lost. Reconnecting...");
            wifi_status = WL_IDLE_STATUS;
            wifiConnect();
        }

    }

    // connect to WiFi (or reconnect if WiFi lost)
    void wifiConnect(){

        // check for the presence of the shield:
        if (WiFi.status() == WL_NO_SHIELD) {
            Serial.println("WiFi chip not present or accessible");
            // don't continue:
            while (true);
        }

        // attempt to connect to WiFi network:
        while ( wifi_status != WL_CONNECTED) {
            Serial.print("Attempting to connect to SSID: ");
            Serial.println(ssid);
            // Connect to WiFi:
            if (sizeof(pass) <= 1) {
            wifi_status = WiFi.begin(ssid);
            }
            else {
            wifi_status = WiFi.begin(ssid, pass);
            }

            // wait 2 seconds for connection:
            delay(2000);
        }

        // you're connected now, so print out a success message:
        Serial.println("You're connected to the network");

        // print your Feather's IP address:
        IPAddress ip = WiFi.localIP();
        Serial.print("Device IP Address: ");
        Serial.println(ip);
        
    }
    ```

1. Create a new tab in your sketch:

    1. Find the &#9662; symbol in the upper-right of the Arduino interface and left-click on it.  
    
    1. In the resulting pop-up, choose New Tab.

        _(Alternatively, you can press SHIFT-&#8984;-N)_

    1. You will be prompted to enter a name for this new tab.  Type:

        `arduino_secrets.h`

        and then click "OK".


1.  The new tab will appear in your IDE workspace.  Copy and paste the following code into the `arduino_secrets.h` tab in your sketch:

    ```arduino
    #define SECRET_SSID "DukeOpen"  // your network SSID (name)
    #define SECRET_PASS ""  // your network password; leave empty for open networks
    ```

1.  

## Twitter Setup

To interact with Twitter's API, you will need a developer account and Twitter API keys.

1.  Go to [https://twitter.com/login](https://twitter.com/login) and sign in with your Twitter account credentials.

1.  Visit [https://developer.twitter.com/en/apply/user](https://developer.twitter.com/en/apply/user) to register your Twitter username for developer access.

    1. Your username should be selected by default.  Click "Continue."

    1. Next, select the "I am requesting access for my own personal use" radio button.

    1.  Enter a name for your developer account or project and select Primary Country of Operation.  Click "Continue"

    1.  On the next screen, choose your use case ("Student project / Learning to code").

    1.  Describe your project, such as:

        ```
        1. I am testing the Twitter API with an IoT device (Adafruit Feather M0)
        2. I want my own new Tweets, new followers and likes to my Tweets to trigger functions on my device. (light LEDs, etc)
        3.  No,  simply reading and responding to Twitter data
        4.  Content will not be displayed on device, except abstractly.
        ```

    1.  Select `No` for "Will your product, service, or analysis make Twitter content or derived information available to a government entity?"

    1.  Read and accept the Terms of Service.

    1.  Finally, check your email inbox for a verification email.  In the email, click the "Confirm Your Email" button.

1.  Now you are ready to create your first Twitter app.  Visit [https://developer.twitter.com/en/apps](https://developer.twitter.com/en/apps) and click on the "Create an App" button.

1.  Enter the App details (these can be modified later):

    1.  Name: `vms564s_app`

    1. App Description:  `This app enables connectivity to an IoT device`

    1.  Website URL: `https://aahvs.duke.edu`

    1. Tell us how this app will be used: `This app is for a class project for VMS564S  Physical Computing and the Internet of Things at Duke University.`

1.  Click the Create button.  You will be taken to a summary page.  Verify and/or edit the information as needed.

1. Click on the Permissions tab.  For our purposes, we only need "Read-only" access.  If you want your Arduino app on the Feather to also be able to post Tweets under your account, choose "Read and write".   Click "Save".

1.  Next, click on the "Keys and Tokens" tab.

1.  Under "Access token & access token secret", click "Create".  This will generate both an `Access token` and `Access token secret` that you will need for your Arduino Sketch.  Leave this page open in your browser so you can access these later.  Or return via [https://developer.twitter.com/en/apps](https://developer.twitter.com/en/apps) 


https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/overview








