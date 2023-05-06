Download Link: https://assignmentchef.com/product/solved-eec172-lab4-introduction-to-aws-and-restful-api
<br>
In this lab, you will explore the RESTful API and use it to connect with Amazon Web Services (AWS). First, you will create a ‘thing’ in AWS and use the HTTP GET command to retrieve status information about your thing. You will then use your code from Lab 3 to compose messages with an IR remote, which you will send to AWS using the HTTP POST command. You will use a rule in AWS to send the text to your cellphone using

Amazon’s Simple Notification Service (SNS). Much of the code you will need for accessing AWS will be provided to you. You will need to understand it at a high-level and modify it for your specific AWS account.




<strong>Please view the video </strong><strong><u>AWS IoT Getting Started</u></strong><strong> to understand the basics of AWS IoT. </strong>




<h1>New Equipment Needed</h1>

<ul>

 <li>An Amazon AWS account (You can get a 1-year free trial account)</li>

</ul>

See <u>https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-free-tier.html</u> for information on using the AWS free tier.

<strong> </strong>

<h1>Development Tool Installation Procedure</h1>

To do the lab on your own computer, you will need to install the following programs:




<ol>

 <li><strong>OpenSSL </strong>(<u>https://www.openssl.org/)</u></li>

</ol>

OpenSSL will be needed to convert the .pem formatted certificate and keys to the .der format that is required. If you have other means of doing this, that is fine as well.




<strong>Background on the RESTful API: </strong>

The RESTful API, based on the Representational state transfer (REST) architecture, is a web-based communication protocol that is widely used for many services. The RESTful API is typically implemented using HTTP.  Although not as optimized for low power and low-cost applications as another IoT protocol, MQTT, RESTful API is widely used due to its simplicity and genericity.




The RESTful API defines the format of the messages that are sent to a service to interact with that service. The function of the RESTful APIs will vary depending on the service with which you are interacting. Some of the commonly used commands are listed below.




<strong>Common RESTful API Commands: </strong>

<ul>

 <li><strong>GET</strong> – Often used to retrieve data or information from a service. May also just be used as a simple trigger. For AWS IoT, GET can be used to retrieve a device’s parameters from its shadow.</li>

 <li><strong>POST</strong> – Can be used to update or push data to the cloud or service. For AWS IoT, POST could be used to update a device’s parameters in the device shadow.</li>

 <li><strong>DELETE</strong> – Used to manage and remove data structures in the cloud. For AWS IoT, DELETE can be used to delete a device shadow.</li>

</ul>




Other RESTful API commands may exist, depending on the service, and are based on HTTP commands: <em>HEAD, PUT, TRACE, OPTIONS, CONNECT, PATCH</em>.




For more information on the RESTful API please see: Beginners guide to creating a REST API: <u>http://www.andrewhavens.com/posts/20/beginners-guide-to-creating-a-rest-api</u>




<strong>Background on AWS IoT: </strong>

Amazon Web Services (AWS) is a collection of various web-services for your cloud-based needs and removes the need of individual businesses to develop the hardware infrastructure for these initiatives. Recently, the Internet of Things (IoT) service was released, allowing connection of many hardware products.




To represent these <em>real-world</em> devices in the cloud and maintain a persistent/coherent state with intermittent network connections, AWS IoT has a concept of a device <em>shadow</em>. This shadow represents the <em>real-world</em> device’s state in the cloud. As the device changes states, it pushes its changes to the shadow device on AWS via MQTT or a RESTful API. If the device loses network connection, and then comes back online, the device can pull the last-known state from the shadow to update itself. Updates to this shadow can trigger actions in other services as well using <em>Rules</em>. In this lab, a rule is used to send text messages to your cell phone.

<strong> </strong>

<strong>Part I: Connecting to AWS (securely) and updating your Shadow </strong>

<h1>Part I.A: Setting up an Amazon AWS account</h1>

For this lab, you will need to setup an Amazon AWS account and get a basic understanding of AWS.  One person per team will need to create an AWS account that can be shared for completing this lab.







<h2><strong>Figure 1</strong>: Amazon AWS Console View</h2>




After setting up an account, log into the console.  You should end up seeing a page similar to Figure 1.  After you are logged in, expand the services and click on the <strong>AWS IoT Core</strong> link. If it does not appear, make sure you have selected an Amazon AWS region at which this service is active. We recommend that you use the <strong><u>U.S.</u> <u>West (Oregon)</u></strong> AWS server for this lab.




In the AWS IoT Core service, the controls on the left will list the actions you can do such as Monitor, Onboard,

Manage, Secure and Act.  In the bottom left corner of the screen, there is a <em>Learn </em>button and a link to Amazon’s IoT <em>interactive</em> tutorial. Click on it, and go through the tutorial for a <em>high-level explanation </em>about how AWS IoT uses Rules to do cool things.




<h1>Part I.B: Setting Up Your First Device Thing/Shadow with the AWS IoT Console</h1>

For the next part of the lab, you will need to generate the access keys to allow your CC3200 to connect to your Amazon AWS account securely.  For a <em>real-world </em>device to connect to Amazon AWS you will need to do the following:

<ol>

 <li><strong><u>Create a Device Thing/Shadow</u></strong> This will generate a server-side representation of your <em>real-world</em> device (your thing).</li>

</ol>

<h2>2. <strong><u>Generate the required certificates and keys</u></strong> for establishing an encrypted connection 3. <strong><u>Attach the certificate and keys</u></strong> to the Device Thing/Shadow 4. <strong><u>Give the Device Thing/Shadow permission</u></strong> to use the IoT functions of AWS</h2>




Fortunately, the above can be accomplished using the Amazon Console interface. Click on <strong>Manage, </strong>then <strong>Things</strong> and then <strong>Register a thing</strong> as shown in Figure 2.

<strong>Figure 2:</strong> Creating Your First Thing

You should be presented with a display similar to Figure 3. Click on the button at the bottom to <strong>Create a single thing</strong>.

<strong>Figure 3:</strong> Creating a Single Thing

For your first device thing, it is recommended you give it a descriptive name, such as ‘CC3200_Thing’ or ‘<em>MyIdName</em>_CC3200Board’. Leave the Thing Type, Thing Group and Attribute Key / Value options blank. Once you have entered the device name, click <strong>Next</strong>. When successful you should see something that looks like Figure 4.

<h2><strong>Figure 4:</strong> Creating Certificate for Your Thing</h2>







Click the <strong>Create certificate</strong> button for the One-click certificate creation as shown in Figure 4.




<strong>Figure 5: </strong>Creation of Certificate, Public and Private keys







AWS should generate the required public key, private key, and certificate for identifying and encrypting traffic from your device, and allow you to download them to your computer.  <strong>It is <u>important to download and save</u> <u>these files</u> as you will NOT BE ABLE TO RETRIEVE THE PUBLIC and PRIVATE KEYS after you close the page. </strong>We will provide you with the appropriate root CA file so don’t bother downloading it from AWS. Download and save the certificate, public and private keys to a folder. Later in this tutorial, we will convert them from the .pem format to the .der format and load them into the CC3200’s serial flash.

<strong> </strong>

After downloading, don’t forget to <strong>Activate</strong> the certificate. To learn more about how certificates are used, see: <u>https://realtimelogic.com/blog/2013/10.</u> Finally, once you have activated your certificate and keys, click the

<strong>Attach a policy</strong> button. Since we don’t have any existing policies, just click on <strong>Register a Thing</strong> on the next window. You have now registered your thing with an active certificate, but we still need to create and attach a policy to the certificate.




<h1>Part I.C: Making a Policy to Allow Update/Get Status from Thing Shadows</h1>

To represent your <em>thing</em> (i.e., your CC3200 board) in the cloud, AWS uses the concept of a <em>device shadow</em> to which you can get the last state or push a new state to the server. Therefore, with intermittent connection to a wireless network, your device could pull the latest state from the cloud and maintain continuity. However, appropriate security measures can be made on AWS to ensure that the devices have the <em>privilege</em> to perform the actions requested. These next steps will walk you through how to do this:




From the AWS IoT console, click on <strong>Secure</strong> and then <strong>Policies</strong>, as shown in Figure 6

Click on <strong>Create a policy</strong>. This should bring up a window similar to Figure 7.







<h2><strong>Figure 7:</strong> Creating a Policy</h2>




Give your policy a descriptive name, and add the actions “iot:GetThingShadow” and “iot:UpdateThingShadow”. Furthermore, update the Amazon Resource Number (ARN) to signify that these actions apply to your <em>thing </em>created previously. Be sure to select the <em>Allow</em> Effect and then click on <strong>Create</strong>. You can learn more about the actions and other access management modules in the AWS documentation (<u>http://docs.aws.amazon.com/IAM/latest/UserGuide//access_policies.html</u>).




Now that you have created a policy, you need to attach it to your certificate. From the AWS IoT console, select <strong>Secure</strong> and <strong>Certificates</strong> and then click on you’re the certificate you recently created. From the Actions pulldown menu, click on <strong>Attach policy</strong> as shown in Figure 8.







<strong>Figure 8: Attaching Policy to Certificate </strong>




Select the policy that you just created and then click on <strong>Attach</strong>. Now your policy should be successfully attached to the certificate for your thing. This will allow you to use the GET and POST commands via REST from the device associated with this certificate.




<h1>Part I.D: Converting the Keys/Certificates for Use with the CC3200</h1>

In this part of the lab, you will convert the private key and certificates to another format. This is necessary since AWS uses the .pem format, while your CC3200 uses the .der format.




The connection security between the CC3200 board and AWS is guaranteed through the TLS protocol. There are four components involved, the <strong>public</strong> and <strong>private key</strong>, a <strong>certificate</strong> and a <strong>root certificate</strong>. While the private key is used to decipher encrypted messages, the certificate is used to validate the public key. Certificates are built on a chain of trust, and so there is a root certificate which is used to sign the certificate.




The key and certificates can exist in either .pem or .der format. The former has ASCII-readable characters (unsecure, readable), while the latter is in binary and more compact (still unsecure, but harder to read). Your generated files in AWS are in the .pem format but the CC3200 requires the .der format.




To convert the files from .pem to .der format, we will use openSSL, an open source library for transport layer security. You need to open a command prompt and navigate to the openSSL installation directory. The commands that are needed are the following:

<strong> </strong>

<u>For the Root Certificate Authority (rootCA) file and the certificate file</u>

<strong>&gt;</strong>openssl x509 -outform der -in C:…rootCA.pem -out C:…rootCA.der

(Note: we will provide the rootCA.der file)




<strong>&gt;</strong>openssl x509 -outform der -in C:…certificate.pem.crt -out C:…client.der




<u>For the Private Key File:</u>

&gt;openssl rsa -outform der -in C:…private.pem.key -out C:…private.der

<strong> </strong>

You will obviously need to modify your file names and file paths to where you saved your keys/certificates. If necessary, move the files that need to be converted into the same file directory in which openSSL was installed. The rootCA file will be shared among all of the CC3200s used for the project.  The client and private keys should be unique to each device. It is highly recommended to group or name the files accordingly so that the keys/certificate pairs do not get mixed up or over-written.




<h1>Part I.E. Using UniFlash to flash key and certificates to CC3200</h1>

To enable a secure connection from your CC3200, you need to download the private key and certificates to the serial flash on your board. You will do this using the UniFlash utility, as described in the Uniflash tutorial.




You should flash the .der formatted certificates, <em>root certificate</em> (used for authenticating the AWS server), <em>client certificate</em> (used by AWS to authenticate our device), and the <em>private key</em> (for device-side encryption) to <strong>User Files</strong> using the UniFlash utility, as shown in Figure 9. You will specify the path to those files on your PC in the Url box and select the Erase, Update, and Verify checkboxes, and click <em>Program</em> to flash those files to the board. You can also load the application (which you will develop in a later part) binary into /sys/mcuimg.bin or you can download it from Code Composer Studio and run it from the Debugger. Take note of the path of your files on the flash (i.e. – the ‘Name’ field below) as you will need to reference them in your code. If you forget them, you can list the files on your flash by clicking on <em>List Files </em>in UniFlash.  Be sure to place jumper on SOP2 for flashing.







<strong>Figure 9: Sample Setup to Flash a RootCA, Private Key and Certificate </strong>













<h1>Part I.F. Accessing AWS using the RESTful API</h1>

Amazon’s RESTful API for AWS IoT provides access to <em>device shadows</em>, or <em>thing shadows</em>.  These are JavaScript Object Notation (JSON) formatted data that allow state information for AWS things to be stored and retrieved, even when a device might be disconnected from the service. To store or retrieve data from a device shadow, Amazon provides special MQTT feeds and RESTful API calls.




Select your device thing from the AWS IoT console and click on <strong>Interact</strong> as shown in Figure 10.







<h2><strong>Figure 10: </strong>Rest API Endpoint for Your Thing</h2>




From this page, you can get the Rest API endpoint for your thing as well as MQTT topics that will allow you to update your thing’s shadow. The REST API endpoint displays the web address for the AWS Thing/Shadow, and contains the web address of your account’s AWS Endpoint. For now, copy the endpoint address into a word or text file, and save it for reference. You will need it later to connect to the AWS server.  Below is an example Restful API endpoint address.  The initial string of characters will be some randomly generated string generated for your account by Amazon.




Endpoint:

<em>identifier</em>.iot.<em>region</em>.amazonaws.com




Example:

https://xxxxxxxxxxxxxx.iot.us-west-2.amazonaws.com




Updating State Information to your Device Shadow:

<em>https://</em><em>endpoint</em>/things/<em>thingName</em>/shadow







<h1><u>Example GET Method</u><u> (GetThingShadow)</u></h1>




GET /things/<em>thingName</em>/shadow HTTP/1.1

Host: <em>identifier</em>.iot.<em>region</em>.amazonaws.com

Connection: Keep-Alive







<h1><u>Example POST Method (UpdateThingShadow)</u></h1>




POST /things/<em>thingName</em>/shadow HTTP/1.1

Host: <em>identifier</em>.iot.<em>region</em>.amazonaws.com

Connection: Keep-Alive

Content-Type: application/json; charset=utf-8

Content-Length: 30

{

“state”:{

“desired”:{

“color”:”green”

}

}

}




To send a RESTful API POST message to AWS, the service must first be connected to with the SimpleLink socket library. The initial connection should be to the base address for the service or website with which you are trying to communicate.  For example, you may have an AWS device thing that is said to have the RESTful API address of the following: https://&lt;endpoint&gt;.iot. us-west-2.amazonaws.com/things/CC3200_Thing/shadow.  The initial connection with the socket library should only be to &lt;endpoint&gt;. iot. us-west-2.amazonaws.com. The HTTPS part of the address will be managed based on the socket credentials, and the AWS device thing subdirectory will be used in the RESTful API calls.

<strong> </strong>

<h2><strong>Connection Configuration for AWS</strong></h2>

<ul>

 <li>2</li>

 <li>Cipher: TLS ECDHE RSA with AES 256 CBC SHA</li>

 <li>Port: 8443</li>

 <li>Certificates needed: Certificate Authority file for AWS (rootCA.der), x509 Device Certificate file (client.der) and the matching private key file (private.der)</li>

</ul>







The RESTful API application requires Internet connectivity through an Access Point (AP). The AP details are contained in the header file common.h. Modify the common.h header file, if necessary, to match the following configuration for the AP in the lab:




// Values for below macros shall be modified as per access-point(AP) properties

// SimpleLink device will connect to following AP when application is executed //

<strong>#define</strong> SSID_NAME           “eec172”          /* AP SSID */

<strong>#define</strong> SECURITY_TYPE       SL_SEC_TYPE_OPEN /* Security type (OPEN or WEP or WPA*/

<strong>#define</strong> SECURITY_KEY        “”               /* Password of the secured AP */

<strong>#define</strong> SSID_LEN_MAX        32

<strong>#define</strong> BSSID_LEN_MAX       6




Once common.h has been modified, you can build and test the example program. Please note that common.h does not reside in your workspace, so if you work on the lab computers <em>and </em>your personal laptop you will need to modify them in both places, and with the appropriate settings for the wireless network you are trying to connect with.

<strong> </strong>

Another important change is that the secure client application must have the “current” date and time so that the credentials can be verified as valid. In this example, we use variables in the main program to update the date and time. The date and time only need to be current within the last month or so. Having the date and time hardcoded into the program is obviously not a robust or secure programming method since the code will eventually stop working unless periodically updated. However, in the interest of reducing complexity, we will use this method for this lab. The date and time variables are stored in the following constants in main.c:




//NEED TO UPDATE THIS FOR IT TO WORK!

<strong>#define</strong> DATE                26   /* Current Date */

<strong>#define</strong> MONTH               01     /* Month 1-12 */

<strong>#define</strong> YEAR                2019  /* Current year */

<strong>#define</strong> HOUR                17    /* Time – hours */

<strong>#define</strong> MINUTE              0    /* Time – minutes */

<strong>#define</strong> SECOND              0     /* Time – seconds */




Now you can connect your CC3200 securely to AWS. Demonstrate that you can use a POST request to post state to your AWS thing and use a GET request to obtain status information on your AWS thing. You should receive an HTTP 200 status code, showing a successful POST or GET request, as well as some basic status information about your device thing.




<strong>NOTE</strong>: you need to give your thing shadow some initial state before you can do an HTTP GET on it. You can give the shadow state by using the HTTP POST command or by using the AWS web interface. Also, update the time in main.c everytime you run your program. The time difference of few minutes should be ok. In common.h file, SSID_Name is the name of your WiFi. We suggest that you use portable WiFi hotspot on your device with open security. The CC3200 doesn’t require a lot of data to work with AWS. If this is not possible, then change SECURITY_TYPE to match to your WiFi and enter the WiFi password in SECURITY_KEY.




In this part, you can start with the working example project <strong>SSL_REST_API_AWS_W19.zip</strong>. You will need to examine it carefully to understand it and modify it appropriately. In particular, you should examine the http_post() function to see how the HTTP command is sent to AWS. The data must be formatted properly, or AWS (or any other web service) will not accept it as valid. In order to use this project on your system, you may need to modify the following files or parameters:

<ol>

 <li>h – as described above for the eec172 Access Point.</li>

 <li>c – you will need to use your own AWS endpoint path and thing name as well as changing the HTTP command in order to test both GET and POST. You should also change the message data that you post to your thing. However, be careful to keep the correct data format. You may also need to update the date, as described above.</li>

 <li>c – verify that the pinmux.c file sets up the pins that you intend to use. For the default application, only the UART0 and GPIO signals driving the on-board LED are actually used. If your program uses the same pins as the on-board LEDs, you will have problems.</li>

 <li>Flash the private key, the client certificate and the root Certificate Authority into the CC3200 flash, as described.</li>

</ol>










<strong>             </strong>

<strong>Part II:  Using SNS to send a text to your phone</strong>

For this project you are to <u>compose a text message using your IR remote and send it to your phone</u>. This will be accomplished by setting up a topic in the Simple Notification Service and registering your text-capable cell phone, and establishing a Rule in the IoT service to trigger when you update your device shadow and send messages to your phone. The interactive tutorial you went through in <strong>Part I.A </strong>gives you a high-level overview on how this is accomplished in AWS.




For this lab we recommend that you use the “US West (Oregon)” AWS server. You need to use a server that runs both the IoT and SNS services.







To do this you must perform the following:

<ol>

 <li>Create a thing for the cc3200 as you have done before.</li>

 <li>Create an SNS topic</li>

 <li>Subscribe to the SNS topic with your phone number and the SMS option.</li>

 <li>Create a rule to forward incoming thing messages to your SNS topic.</li>

 <li>Use the REST API to post to your topic.</li>

</ol>




We will walk you through <strong><u>creating an SNS topic</u></strong> and <strong><u>creating a rule.</u></strong>




<strong>Part II.A. Creating an SNS Topic:</strong>

<strong> </strong>

Amazon Web Services has a collection of different web services which can be integrated to perform a larger function. So far you have been using the IoT service to communicate the states of <em>real-world devices or things</em> to the cloud. The Simple Notification Service (SNS) is another module that will allow you to push messages to subscribers.  Navigate to the SNS service by expanding the services tab and finding Simple Notification System under <em>Application Integration</em> as shown in Figure 11.







<h3><strong>Figure 11: </strong>Simple Notification Service in Menu of AWS Services</h3>







From the SNS dashboard, click on <strong>Create topic </strong>as shown in Figure 12.







<h3><strong>Figure 12: </strong>SNS Dashboard</h3>




Next, choose a Topic name and a Display name of no more than 10 characters and then click <strong>Create topic</strong>, as shown in Figure 13.







<h3><strong>Figure 13: </strong>Creating an SNS Topic</h3>




Afterwards, <strong><u>Create a Subscription</u></strong>, where the protocol is <em>SMS</em> (Simple <em>Messaging</em> Service) for text messages.

The endpoint is your cell-phone number to receive text messages. You can then <strong><u>test</u></strong> to make sure this module works by clicking on the ‘<strong>Publish to topic</strong>’ button and manually sending a text message from the AWS Console to your cell-phone (use RAW format for now).







<h3><strong>Figure 14: </strong>Publish a Message to your SNS Topic</h3>







<strong>Part II.B. Creating an IoT Rule: </strong>

<strong> </strong>

The next step is to create an IoT Rule that will trigger when you push updates to your shadow device. Earlier in the lab, we updated our device shadow (state) via the REST API. When our device shadow is updated, the IoT module publishes messages to several reserved Message Queue Telemetry Transport (<u>MQTT</u>) topics, a publishsubscribe ‘lightweight’ messaging protocol. In order to trigger your rule, we will be listening for updates to the device shadow via these topics. A full list of reserved topics that can be subscribed to can be found at <u>http://docs.aws.amazon.com/iot/latest/developerguide/thing-shadow-mqtt.html</u> .




From the AWS IoT console, click on <strong>Act</strong> and then click on <strong>Create a Rule</strong>, as shown in Figure 15.




<h3><strong>Figure 15:</strong> Creating a Rule</h3>




You should listen to the topic ‘$aws/things/<em>thingName</em>/shadow/update<sub>/accepted’ </sub>(the $ and the single quotes should be <em>included</em>), report on the attribute ‘state.desired’, and leave the condition blank. Then attach an action that will send an SNS push notification when the rule is triggered. Select the SNS target to be the topic you created previously, and use the ‘RAW’ message format (for now). You will also need to create an IAM role to allow the IoT to access the SNS service ‘securely’ (you can learn more about Identity Access and Management [IAM] roles here: <u>http://docs.aws.amazon.com/iot/latest/developerguide/iam-users-groupsroles.html</u>, but will not need to understand them for this lab). Your SQL query statement for your rule should be similar to the figure below.







<h3><strong>Figure 16:</strong> Setting up your IoT Rule</h3>




Make sure the rule is <u>enabled</u> and try to push an update to your device shadow using your CC3200,… in a couple of seconds you <em>should</em> get a text message. Once that works, you can clean up the formatting using JSON message format SNS action. To figure out how to do this, go back to the SNS topic you made, click the <em>Publish a topic</em> button, and experiment with the <u>JSON message generator</u> to find the appropriate syntax. <u>Investigate</u> how you will need to modify your SQL statement to ‘clean-up’ your message. Helpful link and subsections for SQL: <u>http://docs.aws.amazon.com/iot/latest/developerguide/iot-sql-reference.html</u>







<h3><strong>Figure 17: </strong>Updating Device Shadow from CC3200</h3>







<h3><strong>Figure 18:</strong> Verifying Device Shadow State</h3>







<strong>Part II.C. Integrate your game to send a message to your phone: </strong>

Now that you have the SNS module and IoT Rules set up appropriately, you can <u>start integrating your Lab 3</u> <u>code to send a text to your phone when the game is finished</u>. Start with the program for connecting to AWS via the REST API that you implemented in the last part. When you are done, you will be sending a text from your CC3200 through the cloud to your cellphone. Cool, yeah?




The Lab 3 has the game, which ends when the ball touches the boundary of the OLED Screen. At this point you can simply text Game Ended! to your phone.




<strong>Note: </strong>If the SMS is not getting delivered due to some issues, you can also use email. This step has to be done while <strong><u>Create a Subscription.</u></strong>




The following links provide useful information on AWS.




Amazon IoT Rules Documentation: <u>http://docs.aws.amazon.com/iot/latest/developerguide/</u><u>iot</u><u>-rules.html</u> Amazon Device Shadows Documentation: <u>http://docs.aws.amazon.com/iot/latest/developerguide/iot-thingshadows.html</u>




<u>Demonstrate your working program to your TA and have him sign your verification sheet.</u>







<h1><u>Lab Report</u></h1>

<strong> </strong>

<ol>

 <li>The video in *.mp4 format showcasing the Program that sends the message to the Cell phone/Email using AWS service. The video should also capture the messages printed via UART on the terminal. Please summarize your understanding of the <strong><u>all AWS modules</u></strong> we have used, and how the program is working.</li>

</ol>




<ol start="2">

 <li>The video should also capture you while explaining and demonstrating the working of the circuit. If you have made extra connections, please explain it to us through the video. For example, you can ask someone at home to film you or use the selfie camera to record yourself explaining the working and then use the back camera to capture the demonstration of the circuit. You can also create your own PowerPoint slides and walk us through it for explaining the circuit connection and your idea.</li>

</ol>




<ol start="3">

 <li>Try to limit the video to maximum of 10 minutes. The video and audio clarity should be decent enough for us to judge your work.</li>

</ol>




<ol start="4">

 <li>Zip the source files and video and upload it to the canvas, make sure the zip is not locked and the source files can be opened using text editor software. Please comment the code wherever necessary.</li>

</ol>




<ol start="5">

 <li>25 points will be deducted for late submissions <strong>02/25 11:59 PM through 02/28 11:59 PM</strong>, and submission after <strong>02/28 11:59 PM</strong> will not be accepted/graded.</li>

</ol>




<strong>References and Links:</strong>




CC32xx SSL Demo Application:

<u>http://processors.wiki.ti.com/index.php/CC32xx_SSL_Demo_Application</u>




Configuring the AWS CLI client to connect to the Amazon Servers: <u>http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html</u>




Tutorial for using the Identity and Access Management Guide (This is used to generate the credentials to connect via AWS CLI):

<u>http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html</u>




IAM tutorial link:

<u>http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html</u>




Amazon IoT Quickstart Guide Link: <u>https://docs.aws.amazon.com/iot/latest/developerguide/iot-quickstart.html</u> <u>http://docs.aws.amazon.com/iot/latest/developerguide/protocols</u>