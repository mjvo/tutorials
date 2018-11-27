---
title: Instagram
description: Querying the Instagram API from the Feather
authors:  Mark Olson
updated:  2018-11-25
---

## About this Guide

Some of your projects require querying the API of an online service (such as Instagram) and then parsing the data sent back from the service.   [JSON](https://www.json.org/) (JavaScript Object Notation) is a common data format returned by API queries.  This guide provides instructions for configuring your Instagram account to enable API requests and provides sample code for querying and parsing that data for use by your Feather.  
 

## Install ArduinoJSON Library

First, if you have not done so alreadyinstall Benoit Blanchon's [ArduinoJson library](https://arduinojson.org/).   Proceed with the steps below and/or consult arduinojson.org's [official documentation](https://arduinojson.org/v5/doc/installation/).  

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

By now you should be familiar with this section, which outlines the Arduino code necessary to connect the Feather M0 to a WiFi access point.  We'll use this as a starting point for our sketch.

1. Create a new Arduino sketch by choosing File --> New from the main menu.

1. Save it by choosing File --> Save and give it a memorable name, such as `InstagramQuery`

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

1. Return to the main tab of your Arduino sketch.  

1. _Replace_ the contents of that tab with the code provided at this Github gist:

    <script src="https://gist.github.com/mjvo/c5ca35bebfe7807b540dc8b723e95328.js"></script>



## Instagram Setup

### Create a Client

To interact with Instagram's API, you will ultimately need an `access token`.  Unfortunately, getting one is not a straightforward process and will change when Instagram fully deprecates its API in 2020.   For now, follow these steps:

1.  Go to [https://www.instagram.com/accounts/login/](https://www.instagram.com/accounts/login/) and sign in with your Instagram account credentials.

1.  Next, visit [https://www.instagram.com/developer/](https://www.instagram.com/developer/) to access the API and its documentation.  Here you will set up a "client" that will allow authorized users (yourself) to query Instagram using the API.  You will use this in "sandbox mode" -- for testing purposes only.  If you wanted your application to be available to the general public you would need to have it vetted by Instagram.

    1. From the main menu on the page, click "Manage Clients"

    1. On the next page, click the green "Register a New Client" button.

    1. On the "Register a New Client ID" page, you will be prompted to enter information about your application:

        * Application Name:  `FeatherQuery` (or another name you choose)

        * Description: `Enabling Feather M0 IoT device to query the Instagram API for data on my own image posts.`

        * Company Name: _Your Name_

        * Website URL:  Use your own URL if you have a website; if not, use `https://www.duke.edu`

        * Valid redirect URIs:   Again, use your own url; if not, use `https://www.duke.edu`]

        * Privacy Policy URL:  Leave blank (not required for sandbox testing)

        * Contact email:  _your email_

    1. Finally, click the green "Register" button

1. The client you just created will appear in your Manage Clients list.

### Generate Access Token

Next we need to use the API to generate an `access token` that we will use in our Arduino code.  This is a multi-step process.

1. First, click on the blue "Manage" button next to the name of the Client you just created.

1.  Go to the "Security" tab and **un**check the tickbox next to "Disable implicit OAuth:"

1. Click the green "Update Client" button

1.  Return to the "Details" tab by clicking again on the blue "Manage" button next to the name of the Client.

1. Note at the top of the page there is a "Client ID" and "Client Secret."  Both are long strings of characters.

1. Leaving this page open, open a new tab in your web browser.

1. Navigate to the following URL, where `CLIENT-ID` is replaced with your client ID from the previous tab and where `REDIRECT-URI` is replaced with redirect URI you specified when creating the client. 

    ```

    https://api.instagram.com/oauth/authorize/?client_id=CLIENT-ID&redirect_uri=REDIRECT-URI&response_type=token
    
    ```

1. If successful, you should be taken to an Instagram authorization page for your client:
    ![Instagram Authorization Page](Instagram_OAuth.png)


    !!! failure
        If you instead receive an error message, note the error message and correct accordingly:
        
        "Invalid Client ID"
            Go to the "Details" tab of your Client and double-check the accuracy of your client ID.  No spaces are allowed.  Re-copy and paste again if necessary.

        "Redirect URI does not match registered redirect URI"
            Go to the "Security" tab of your client and verify the accuracy of the redirect URI.

        "Implicit authentication is disabled"
            Return to the "Security" tab of your client and be sure to **un**check the box next to "Disable implicit OAuth:"

1.  Click the green "Authorize" button to authorize the Client to access your Instagram account.

1. Your browser will redirect to the URL you specified as a "Redirect URI" but will append `#access_token=` followed by a long string.  Copy the string of characters that follows the equals sign. 

1.  Return to the Arduino app and paste this access token string into your Arduino sketch under the `arduino_secrets.h` tab.  Create a new [defined constant](https://www.arduino.cc/reference/en/language/structure/further-syntax/define/) named `ACCESS_TOKEN` to hold that access token string:

    ```arduino hl_lines="4"

    #define SECRET_SSID "DukeOpen"  // your network SSID (name)
    #define SECRET_PASS ""  // your network password; leave empty for open networks

    #define ACCESS_TOKEN "token goes here" // Instagram access token
    ```

    Be sure to save your sketch.

1. Finally, return to your browser and test your token's ability to access your account via an https:// GET request to the following URL (where `ACCESS_TOKEN` is replaced by your access token string.):

    ```
    https://api.instagram.com/v1/users/self/?access_token=ACCESS_TOKEN

    ```

1. If successful, your browser should return JSON data about your account, such as your account ID, username, number of posts (media), number of followers (followed_by), etc.

### Arduino Query

Go ahead and test the code on your Arduino.  Be sure to change the SSID and password if you're not on Duke's network.  

Once uploaded, open up the Arduino serial monitor.  You should observe the following behavior:

1. The Feather connects to wifi and is given an IP address.

1. The Feather queries your Instagram account and returns a welcome message and your current number of followers.

1.  The Feather queries your Instagram account and returns your latest post's ID and number of Likes.

1.  Then, every minute, the Feather will query both your followers and your posts.  It will indicate a new follower, new Likes, or a new post.




