# ESP8266-WEATHER-STATION

Written by Last Minute engineers
Visit lastminuteengineers.com for full tutorials

In this project we will be using :
BME280 Temperature, Humidity and Pressure Sensor
ESP8266 NodeMCU

Manage your Arduino libraries and add as necessary

ESP8266WebServer
Adafruit Unified Sensor
Adafruit BME280


Create A Simple ESP8266 Weather Station With BME280
ESP8266 Weather Station Tutorial For Interfacing BME280 Sensor
Don’t let the smartphone weather apps or commercial weather stations(that feeds you with data from stations based miles away) ruin your outdoor plans. With this IoT project you can be your own weatherman!

This project uses ESP8266 NodeMCU as the control device that easily connects to existing WiFi network & creates a Web Server. When any connected device accesses this web server, ESP8266 reads in temperature, humidity, barometric pressure & altitude from BME280 & sends it to the web browser of that device with a nice interface. Excited? Let’s get started!

BME280 Temperature, Humidity and Pressure Sensor
First, let’s take a quick look at the BME280 module.

BME280 is the next-generation digital temperature, humidity and pressure sensor manufactured by Bosch. It’s a successor to sensors like BMP180, BMP085 or BMP183.

BME280 Temperature Humidity Pressure Altitude Sensor Specifications
The operating voltage of the BME280 module is from 3.3V to 5V – Perfect for interfacing with 3.3V microcontrollers like ESP8266.

The module features a simple two-wire I2C interface for communication. The default I2C address of the BME280 module is 0x76 and can be changed to 0x77 easily with this procedure.

Wiring BME280 Sensor to ESP8266 NodeMCU
Connections are fairly simple. Start by connecting VIN pin to the 3.3V output on the ESP8266 NodeMCU and connect GND to ground.

Next, Connect the SCL pin to the I2C clock D1 pin on your ESP8266 and connect the SDA pin to the I2C data D2 pin on your ESP8266.

The following diagram shows you how to wire everything.

Fritzing Wiring ESP8266 & BME280 Temperature Humidity Pressure Sensor
Wiring ESP8266 & BME280 Temperature Humidity Pressure Sensor
Preparing the Arduino IDE
There’s an add-on for the Arduino IDE that allows you to program the ESP8266 NodeMCU using the Arduino IDE. Follow below tutorial to prepare your Arduino IDE to work with the ESP8266, if you haven’t already.

Tutorial of Programming ESP8266 in Arduino IDE
Insight Into ESP8266 NodeMCU Features & Using It With Arduino IDE
The Internet of Things (IoT) has been a trending field in the world of technology. It has changed the way we work. Physical objects and...
Installing Library For BME280
Communicating with a BME280 module is a bunch of work. Fortunately, Adafruit BME280 Library was written to hide away all the complexities so that we can issue simple commands to read the temperature, relative humidity & barometric pressure data.

To install the library navigate to the Arduino IDE > Sketch > Include Library > Manage Libraries… Wait for Library Manager to download libraries index and update list of installed libraries.

Filter your search by typing ‘bme280’. There should be a couple entries. Look for Adafruit BME280 Library by Adafruit. Click on that entry, and then select Install.

Installing BME280 Library In Arduino IDE
The BME280 sensor library uses the Adafruit Sensor support backend. So, search the library manager for Adafruit Unified Sensor and install that too (you may have to scroll a bit)

Adafruit Unified Sensor Library Installation
Displaying Temperature, Humidity, Pressure & Altitude On ESP8266 Web Server
Now, we are going to configure our ESP8266 into Station (STA) mode, and create a web server to serve up web pages to any connected client under existing network.

If you want to learn about creating a web server with ESP8266 in AP/STA mode, check this tutorial out.

Creating Simple ESP8266 Webserver in Arduino IDe using Access Poin & Station mode
Create A Simple ESP8266 NodeMCU Web Server In Arduino IDE
Over the past few years, the ESP8266 has been a growing star among IoT or WiFi-related projects. It’s an extremely cost-effective WiFi module that -...
Before you head for uploading the sketch, you need to make one change to make it work for you. You need to modify the following two variables with your network credentials, so that ESP8266 can establish a connection with existing network.

const char* ssid = "YourNetworkName";  // Enter SSID here
const char* password = "YourPassword";  //Enter Password here
Once you are done, go ahead and try the sketch out.

#include <ESP8266WebServer.h>
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BME280.h>

#define SEALEVELPRESSURE_HPA (1013.25)

Adafruit_BME280 bme;

float temperature, humidity, pressure, altitude;

/*Put your SSID & Password*/
const char* ssid = "YourNetworkName";  // Enter SSID here
const char* password = "YourPassword";  //Enter Password here

ESP8266WebServer server(80);              
 
void setup() {
  Serial.begin(115200);
  delay(100);
  
  bme.begin(0x76);   

  Serial.println("Connecting to ");
  Serial.println(ssid);

  //connect to your local wi-fi network
  WiFi.begin(ssid, password);

  //check wi-fi is connected to wi-fi network
  while (WiFi.status() != WL_CONNECTED) {
  delay(1000);
  Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected..!");
  Serial.print("Got IP: ");  Serial.println(WiFi.localIP());

  server.on("/", handle_OnConnect);
  server.onNotFound(handle_NotFound);

  server.begin();
  Serial.println("HTTP server started");

}
void loop() {
  server.handleClient();
}

void handle_OnConnect() {
  temperature = bme.readTemperature();
  humidity = bme.readHumidity();
  pressure = bme.readPressure() / 100.0F;
  altitude = bme.readAltitude(SEALEVELPRESSURE_HPA);
  server.send(200, "text/html", SendHTML(temperature,humidity,pressure,altitude)); 
}

void handle_NotFound(){
  server.send(404, "text/plain", "Not found");
}

String SendHTML(float temperature,float humidity,float pressure,float altitude){
  String ptr = "<!DOCTYPE html> <html>\n";
  ptr +="<head><meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0, user-scalable=no\">\n";
  ptr +="<title>ESP8266 Weather Station</title>\n";
  ptr +="<style>html { font-family: Helvetica; display: inline-block; margin: 0px auto; text-align: center;}\n";
  ptr +="body{margin-top: 50px;} h1 {color: #444444;margin: 50px auto 30px;}\n";
  ptr +="p {font-size: 24px;color: #444444;margin-bottom: 10px;}\n";
  ptr +="</style>\n";
  ptr +="</head>\n";
  ptr +="<body>\n";
  ptr +="<div id=\"webpage\">\n";
  ptr +="<h1>ESP8266 Weather Station</h1>\n";
  ptr +="<p>Temperature: ";
  ptr +=temperature;
  ptr +="&deg;C</p>";
  ptr +="<p>Humidity: ";
  ptr +=humidity;
  ptr +="%</p>";
  ptr +="<p>Pressure: ";
  ptr +=pressure;
  ptr +="hPa</p>";
  ptr +="<p>Altitude: ";
  ptr +=altitude;
  ptr +="m</p>";
  ptr +="</div>\n";
  ptr +="</body>\n";
  ptr +="</html>\n";
  return ptr;
}
Accessing the Web Server
After uploading the sketch, open the Serial Monitor at a baud rate of 115200. And press the EN button on NodeMCU. If everything is OK, it will output the dynamic IP address obtained from your router and show HTTP server started message.

ESP8266 Station Mode Web Server IP Address On Serial Monitor
Next, load up a browser and point it to the IP address shown on the serial monitor. The ESP8266 should serve up a web page showing temperature, humidity, pressure and altitude from BME280.

BME280 Readings on ESP8266 Web Server
Detailed Code Explanation
The sketch starts by including following libraries.

ESP8266WebServer.h library provides ESP8266 specific WiFi methods we are calling to connect to network. It also has some methods available that will help us setting up a server and handle incoming HTTP requests without needing to worry about low level implementation details.
Wire.h library communicates with any I2C device not just BME280.
Adafruit_BME280.h & Adafruit_Sensor.h libraries are hardware-specific libraries which handles lower-level functions.
#include <ESP8266WebServer.h>
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BME280.h>
Next we create an object of the sensor and variables to store temperature, humidity, pressure and altitude.

#define SEALEVELPRESSURE_HPA (1013.25)

Adafruit_BME280 bme;

float temperature, humidity, pressure, altitude;
As we are configuring ESP8266 in Station (STA) mode, it will join existing WiFi network. Hence, we need to provide it with your network’s SSID & Password. Next we start web server at port 80.

/*Put your SSID & Password*/
const char* ssid = "YourNetworkName";  // Enter SSID here
const char* password = "YourPassword";  //Enter Password here

ESP8266WebServer server(80);
Inside Setup() Function
Inside Setup() Function we configure our HTTP server before actually running it.

First of all, we initialize serial communication with PC and initialize BME object using begin() function. It initializes I2C interface with given I2C Address(0x76) and checks if the chip ID is correct. It then resets the chip using soft-reset & waits for the sensor for calibration after wake-up.

Serial.begin(115200);
delay(100);

bme.begin(0x76);
Now, we need to join the WiFi network using WiFi.begin() function. The function takes SSID (Network Name) and password as a parameter.

Serial.println("Connecting to ");
Serial.println(ssid);

//connect to your local wi-fi network
WiFi.begin(ssid, password);
While the ESP8266 tries to connect to the network, we can check the connectivity status with WiFi.status() function.

//check wi-fi is connected to wi-fi network
  while (WiFi.status() != WL_CONNECTED) {
  delay(1000);
  Serial.print(".");
  }
Once the ESP8266 is connected to the network, the sketch prints the IP address assigned to ESP8266 by displaying WiFi.localIP() value on serial monitor.

Serial.println("");
  Serial.println("WiFi connected..!");
  Serial.print("Got IP: ");  Serial.println(WiFi.localIP());
In order to handle incoming HTTP requests, we need to specify which code to execute when a URL is hit. To do so, we use on method. This method takes two parameters. First one is a URL path and second one is the name of function which we want to execute when that URL is hit.

The code below indicates that when a server receives an HTTP request on the root (/) path, it will trigger the handle_OnConnect function. Note that the URL specified is a relative path.

server.on("/", handle_OnConnect);
We haven’t specified what the server should do if the client requests any URL other than specified with server.on. It should respond with an HTTP status 404 (Not Found) and a message for the user. We put this in a function as well, and use server.onNotFound to tell it that it should execute it when it receives a request for a URL that wasn’t specified with server.on

server.onNotFound(handle_NotFound);
Now, to start our server, we call the begin method on the server object.

server.begin();
Serial.println("HTTP server started");
Inside Loop() Function
To handle the actual incoming HTTP requests, we need to call the handleClient() method on the server object.

server.handleClient();
Next, we need to create a function we attached to root (/) URL with server.on Remember?

At the start of this function, we get the temperature, humidity, pressure & altitude readings from the sensor. In order to respond to the HTTP request, we use the send method. Although the method can be called with a different set of arguments, its simplest form consists of the HTTP response code, the content type and the content.

In our case, we are sending the code 200 (one of the HTTP status codes), which corresponds to the OK response. Then, we are specifying the content type as “text/html“, and finally we are calling SendHTML() custom function which creates a dynamic HTML page containing temperature, humidity, pressure & altitude readings.

void handle_OnConnect() {
  temperature = bme.readTemperature();
  humidity = bme.readHumidity();
  pressure = bme.readPressure() / 100.0F;
  altitude = bme.readAltitude(SEALEVELPRESSURE_HPA);
  server.send(200, "text/html", SendHTML(temperature,humidity,pressure,altitude)); 
}
Likewise, we need to create a function to handle 404 Error page.

void handle_NotFound(){
  server.send(404, "text/plain", "Not found");
}
Displaying the HTML Web Page
SendHTML() function is responsible for generating a web page whenever the ESP8266 web server gets a request from a web client. It merely concatenates HTML code into a big string and returns to the server.send() function we discussed earlier. The function takes temperature, humidity, pressure & altitude readings as a parameter to dynamically generate the HTML content.

The first text you should always send is the <!DOCTYPE> declaration that indicates that we’re sending HTML code.

String SendHTML(float temperature,float humidity,float pressure,float altitude){
  String ptr = "<!DOCTYPE html> <html>\n";
Next, the <meta> viewport element makes the web page responsive in any web browser, while title tag sets the title of the page.

ptr +="<head><meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0, user-scalable=no\">\n";
ptr +="<title>ESP8266 Weather Station</title>\n";
Styling the Web Page
Next, we have some CSS to style the web page appearance. We choose the Helvetica font, define the content to be displayed as an inline-block and aligned at the center.

ptr +="<style>html { font-family: Helvetica; display: inline-block; margin: 0px auto; text-align: center;}\n";
Following code sets color, font and margin around the body, H1 and p tags.

ptr +="body{margin-top: 50px;} h1 {color: #444444;margin: 50px auto 30px;}\n";
ptr +="p {font-size: 24px;color: #444444;margin-bottom: 10px;}\n";
ptr +="</style>\n";
ptr +="</head>\n";
ptr +="<body>\n";
Setting the Web Page Heading
Next, heading of the web page is set; you can change this text to anything that suits your application.

ptr +="<div id=\"webpage\">\n";
ptr +="<h1>ESP8266 Weather Station</h1>\n";
Displaying Readings on Web Page
To dynamically display temperature, humidity, pressure & altitude readings, we put those values in paragraph tag. To display degree symbol, we use HTML entity &deg;

ptr +="<p>Temperature: ";
ptr +=temperature;
ptr +="&deg;C</p>";
ptr +="<p>Humidity: ";
ptr +=humidity;
ptr +="%</p>";
ptr +="<p>Pressure: ";
ptr +=pressure;
ptr +="hPa</p>";
ptr +="<p>Altitude: ";
ptr +=altitude;
ptr +="m</p>";
ptr +="</div>\n";
ptr +="</body>\n";
ptr +="</html>\n";
return ptr;
}
Styling Web Page to Look More Professional
Programmers like us are often intimidated by design – but a little effort can make your web page look more attractive and professional. Below screenshot will give you a basic idea of what we are going to do.

BME280 Readings on ESP8266 Web Server - Professional Look
Pretty amazing, Right? Without further ado, let’s apply some style to our previous HTML page. To start with, copy-paste below code to replace SendHTML() function from the sketch above.

String SendHTML(float temperature,float humidity,float pressure,float altitude){
  String ptr = "<!DOCTYPE html>";
  ptr +="<html>";
  ptr +="<head>";
  ptr +="<title>ESP8266 Weather Station</title>";
  ptr +="<meta name='viewport' content='width=device-width, initial-scale=1.0'>";
  ptr +="<link href='https://fonts.googleapis.com/css?family=Open+Sans:300,400,600' rel='stylesheet'>";
  ptr +="<style>";
  ptr +="html { font-family: 'Open Sans', sans-serif; display: block; margin: 0px auto; text-align: center;color: #444444;}";
  ptr +="body{margin: 0px;} ";
  ptr +="h1 {margin: 50px auto 30px;} ";
  ptr +=".side-by-side{display: table-cell;vertical-align: middle;position: relative;}";
  ptr +=".text{font-weight: 600;font-size: 19px;width: 200px;}";
  ptr +=".reading{font-weight: 300;font-size: 50px;padding-right: 25px;}";
  ptr +=".temperature .reading{color: #F29C1F;}";
  ptr +=".humidity .reading{color: #3B97D3;}";
  ptr +=".pressure .reading{color: #26B99A;}";
  ptr +=".altitude .reading{color: #955BA5;}";
  ptr +=".superscript{font-size: 17px;font-weight: 600;position: absolute;top: 10px;}";
  ptr +=".data{padding: 10px;}";
  ptr +=".container{display: table;margin: 0 auto;}";
  ptr +=".icon{width:65px}";
  ptr +="</style>";
  ptr +="</head>";
  ptr +="<body>";
  ptr +="<h1>ESP8266 Weather Station</h1>";
  ptr +="<div class='container'>";
  ptr +="<div class='data temperature'>";
  ptr +="<div class='side-by-side icon'>";
  ptr +="<svg enable-background='new 0 0 19.438 54.003'height=54.003px id=Layer_1 version=1.1 viewBox='0 0 19.438 54.003'width=19.438px x=0px xml:space=preserve xmlns=http://www.w3.org/2000/svg xmlns:xlink=http://www.w3.org/1999/xlink y=0px><g><path d='M11.976,8.82v-2h4.084V6.063C16.06,2.715,13.345,0,9.996,0H9.313C5.965,0,3.252,2.715,3.252,6.063v30.982";
  ptr +="C1.261,38.825,0,41.403,0,44.286c0,5.367,4.351,9.718,9.719,9.718c5.368,0,9.719-4.351,9.719-9.718";
  ptr +="c0-2.943-1.312-5.574-3.378-7.355V18.436h-3.914v-2h3.914v-2.808h-4.084v-2h4.084V8.82H11.976z M15.302,44.833";
  ptr +="c0,3.083-2.5,5.583-5.583,5.583s-5.583-2.5-5.583-5.583c0-2.279,1.368-4.236,3.326-5.104V24.257C7.462,23.01,8.472,22,9.719,22";
  ptr +="s2.257,1.01,2.257,2.257V39.73C13.934,40.597,15.302,42.554,15.302,44.833z'fill=#F29C21 /></g></svg>";
  ptr +="</div>";
  ptr +="<div class='side-by-side text'>Temperature</div>";
  ptr +="<div class='side-by-side reading'>";
  ptr +=(int)temperature;
  ptr +="<span class='superscript'>&deg;C</span></div>";
  ptr +="</div>";
  ptr +="<div class='data humidity'>";
  ptr +="<div class='side-by-side icon'>";
  ptr +="<svg enable-background='new 0 0 29.235 40.64'height=40.64px id=Layer_1 version=1.1 viewBox='0 0 29.235 40.64'width=29.235px x=0px xml:space=preserve xmlns=http://www.w3.org/2000/svg xmlns:xlink=http://www.w3.org/1999/xlink y=0px><path d='M14.618,0C14.618,0,0,17.95,0,26.022C0,34.096,6.544,40.64,14.618,40.64s14.617-6.544,14.617-14.617";
  ptr +="C29.235,17.95,14.618,0,14.618,0z M13.667,37.135c-5.604,0-10.162-4.56-10.162-10.162c0-0.787,0.638-1.426,1.426-1.426";
  ptr +="c0.787,0,1.425,0.639,1.425,1.426c0,4.031,3.28,7.312,7.311,7.312c0.787,0,1.425,0.638,1.425,1.425";
  ptr +="C15.093,36.497,14.455,37.135,13.667,37.135z'fill=#3C97D3 /></svg>";
  ptr +="</div>";
  ptr +="<div class='side-by-side text'>Humidity</div>";
  ptr +="<div class='side-by-side reading'>";
  ptr +=(int)humidity;
  ptr +="<span class='superscript'>%</span></div>";
  ptr +="</div>";
  ptr +="<div class='data pressure'>";
  ptr +="<div class='side-by-side icon'>";
  ptr +="<svg enable-background='new 0 0 40.542 40.541'height=40.541px id=Layer_1 version=1.1 viewBox='0 0 40.542 40.541'width=40.542px x=0px xml:space=preserve xmlns=http://www.w3.org/2000/svg xmlns:xlink=http://www.w3.org/1999/xlink y=0px><g><path d='M34.313,20.271c0-0.552,0.447-1,1-1h5.178c-0.236-4.841-2.163-9.228-5.214-12.593l-3.425,3.424";
  ptr +="c-0.195,0.195-0.451,0.293-0.707,0.293s-0.512-0.098-0.707-0.293c-0.391-0.391-0.391-1.023,0-1.414l3.425-3.424";
  ptr +="c-3.375-3.059-7.776-4.987-12.634-5.215c0.015,0.067,0.041,0.13,0.041,0.202v4.687c0,0.552-0.447,1-1,1s-1-0.448-1-1V0.25";
  ptr +="c0-0.071,0.026-0.134,0.041-0.202C14.39,0.279,9.936,2.256,6.544,5.385l3.576,3.577c0.391,0.391,0.391,1.024,0,1.414";
  ptr +="c-0.195,0.195-0.451,0.293-0.707,0.293s-0.512-0.098-0.707-0.293L5.142,6.812c-2.98,3.348-4.858,7.682-5.092,12.459h4.804";
  ptr +="c0.552,0,1,0.448,1,1s-0.448,1-1,1H0.05c0.525,10.728,9.362,19.271,20.22,19.271c10.857,0,19.696-8.543,20.22-19.271h-5.178";
  ptr +="C34.76,21.271,34.313,20.823,34.313,20.271z M23.084,22.037c-0.559,1.561-2.274,2.372-3.833,1.814";
  ptr +="c-1.561-0.557-2.373-2.272-1.815-3.833c0.372-1.041,1.263-1.737,2.277-1.928L25.2,7.202L22.497,19.05";
  ptr +="C23.196,19.843,23.464,20.973,23.084,22.037z'fill=#26B999 /></g></svg>";
  ptr +="</div>";
  ptr +="<div class='side-by-side text'>Pressure</div>";
  ptr +="<div class='side-by-side reading'>";
  ptr +=(int)pressure;
  ptr +="<span class='superscript'>hPa</span></div>";
  ptr +="</div>";
  ptr +="<div class='data altitude'>";
  ptr +="<div class='side-by-side icon'>";
  ptr +="<svg enable-background='new 0 0 58.422 40.639'height=40.639px id=Layer_1 version=1.1 viewBox='0 0 58.422 40.639'width=58.422px x=0px xml:space=preserve xmlns=http://www.w3.org/2000/svg xmlns:xlink=http://www.w3.org/1999/xlink y=0px><g><path d='M58.203,37.754l0.007-0.004L42.09,9.935l-0.001,0.001c-0.356-0.543-0.969-0.902-1.667-0.902";
  ptr +="c-0.655,0-1.231,0.32-1.595,0.808l-0.011-0.007l-0.039,0.067c-0.021,0.03-0.035,0.063-0.054,0.094L22.78,37.692l0.008,0.004";
  ptr +="c-0.149,0.28-0.242,0.594-0.242,0.934c0,1.102,0.894,1.995,1.994,1.995v0.015h31.888c1.101,0,1.994-0.893,1.994-1.994";
  ptr +="C58.422,38.323,58.339,38.024,58.203,37.754z'fill=#955BA5 /><path d='M19.704,38.674l-0.013-0.004l13.544-23.522L25.13,1.156l-0.002,0.001C24.671,0.459,23.885,0,22.985,0";
  ptr +="c-0.84,0-1.582,0.41-2.051,1.038l-0.016-0.01L20.87,1.114c-0.025,0.039-0.046,0.082-0.068,0.124L0.299,36.851l0.013,0.004";
  ptr +="C0.117,37.215,0,37.62,0,38.059c0,1.412,1.147,2.565,2.565,2.565v0.015h16.989c-0.091-0.256-0.149-0.526-0.149-0.813";
  ptr +="C19.405,39.407,19.518,39.019,19.704,38.674z'fill=#955BA5 /></g></svg>";
  ptr +="</div>";
  ptr +="<div class='side-by-side text'>Altitude</div>";
  ptr +="<div class='side-by-side reading'>";
  ptr +=(int)altitude;
  ptr +="<span class='superscript'>m</span></div>";
  ptr +="</div>";
  ptr +="</div>";
  ptr +="</body>";
  ptr +="</html>";
  return ptr;
}
If you try to compare this function with the previous one, you’ll come to know that they are similar except these changes.

We have used Google commissioned Open Sans web font for our web page. Note that you cannot see Google font, without active internet connection on the device. Google fonts are loaded on the fly.
ptr +="<link href='https://fonts.googleapis.com/css?family=Open+Sans:300,400,600' rel='stylesheet'>";
The icons used to display temperature, humidity, pressure & altitude readings are actually a Scalable Vector Graphics (SVG) defined in <svg> tag. Creating SVG doesn’t require any special programming skills. You can use Google SVG Editor for creating graphics for your page. We have used these SVG icons.
SVG Icons
Improvement to the Code – Auto Page Refresh
One of the improvements you can do with our code is refreshing the page automatically in order to update the sensor value.

With the addition of a single meta tag into your HTML document, you can instruct the browser to automatically reload the page at a provided interval.

<meta http-equiv="refresh" content="2" >
Place this code in the the <head> tag of your document, this meta tag will instruct the browser to refresh every two seconds. Pretty nifty!

Dynamically load Sensor Data with AJAX
Refreshing a web page isn’t too practical if you have a heavy web page. A better method is to use Asynchronous Javascript And Xml (AJAX) so that we can request data from the server asynchronously (in the background) without refreshing the page.

The XMLHttpRequest object within JavaScript is commonly used to execute AJAX on webpages. It performs the silent GET request on the server and updates the element on the page. AJAX is not a new technology, or different language, just existing technologies used in new ways. Besides this, AJAX also makes it possible to

Request data from a server after the page has loaded
Receive data from a server after the page has loaded
Send data to a server in the background
Here is the AJAX script that we’ll be using. Place this script just before you close </head> tag.

ptr +="<script>\n";
ptr +="setInterval(loadDoc,1000);\n";
ptr +="function loadDoc() {\n";
ptr +="var xhttp = new XMLHttpRequest();\n";
ptr +="xhttp.onreadystatechange = function() {\n";
ptr +="if (this.readyState == 4 && this.status == 200) {\n";
ptr +="document.body.innerHTML =this.responseText}\n";
ptr +="};\n";
ptr +="xhttp.open(\"GET\", \"/\", true);\n";
ptr +="xhttp.send();\n";
ptr +="}\n";
ptr +="</script>\n";
The script starts with <script> tag. As AJAX script is nothing but a javascript, we need to write it in <script> tag. In order for this function to be repeatedly called, we will be using the javascript setInterval() function. It takes two parameters – a function to be executed and time interval (in milliseconds) on how often to execute the function.

ptr +="<script>\n";
ptr +="setInterval(loadDoc,1000);\n";
The heart of this script is a loadDoc() function. Inside this function, an XMLHttpRequest() object is created. This object is used to request data from a web server.

ptr +="function loadDoc() {\n";
ptr +="var xhttp = new XMLHttpRequest();\n";
The xhttp.onreadystatechange() function is called every time the readyState changes. The readyState property holds the status of the XMLHttpRequest. It has one of the following values.

0: request not initialized
1: server connection established
2: request received
3: processing request
4: request finished and response is ready
The status property holds the status of the XMLHttpRequest object. It has one of the following values.

200: “OK”
403: “Forbidden”
404: “Page not found”
When readyState is 4 and status is 200, the response is ready. Now, the content of body (holding temperature readings) is updated.

ptr +="xhttp.onreadystatechange = function() {\n";
ptr +="if (this.readyState == 4 && this.status == 200) {\n";
ptr +="document.body.innerHTML =this.responseText}\n";
ptr +="};\n";
The HTTP request is then initiated via the open() and send() functions.

ptr +="xhttp.open(\"GET\", \"/\", true);\n";
ptr +="xhttp.send();\n";
ptr +="}\n";




Now My working Sketch:-


#include <ESP8266WebServer.h>
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BME280.h>

#define SEALEVELPRESSURE_HPA (1013.25)

Adafruit_BME280 bme;

float temperature, humidity, pressure, altitude;

/*Replace with your SSID & Password in the inverted commas*/ 
const char* ssid = "FRITZ!Box 7490"; // Enter SSID here 
const char* password = "72083987782326629310"; //Enter Password here

ESP8266WebServer server(80);

unsigned long previousMillis = 0; //store last time Adafruit_BME280 was updated 
const long interval = 5000; // Updates Adafruit_BME280 readings every 5 seconds

 
void setup() {
  Serial.begin(115200);
  delay(100);
  
  bme.begin(0x76);   

  Serial.println("Connecting to ");
  Serial.println(ssid);

  //connect to your local wi-fi network
  WiFi.begin(ssid, password);

  //check wi-fi is connected to wi-fi network
  while (WiFi.status() != WL_CONNECTED) {
  delay(1000);
  Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected..!");
  Serial.print("Got IP: ");  Serial.println(WiFi.localIP());

  server.on("/", handle_OnConnect);
  server.onNotFound(handle_NotFound);

  server.begin();
  Serial.println("HTTP server started");

}
void loop() {
  server.handleClient();
}

void handle_OnConnect() {
  temperature = bme.readTemperature();
  humidity = bme.readHumidity();
  pressure = bme.readPressure() / 100.0F;
  altitude = bme.readAltitude(SEALEVELPRESSURE_HPA);
  server.send(200, "text/html", SendHTML(temperature,humidity,pressure,altitude)); 
}

void handle_NotFound(){
  server.send(404, "text/plain", "Not found");
}

String SendHTML(float temperature,float humidity,float pressure,float altitude){
  String ptr = "<!DOCTYPE html>";
  ptr +="<html>";
  ptr +="<head>";
  ptr +="<title>ESP8266 Weather Station</title>";
  ptr +="<head><meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0, user-scalable=no\">\n";
  ptr +="<link href='https://fonts.googleapis.com/css?family=Open+Sans:300,400,600' rel='stylesheet'>";
  ptr +="<style>";
  ptr +="html { font-family: 'Open Sans', sans-serif; display: block; margin: 0px auto; text-align: center;color: #444444;}";
  ptr +="body{margin: 0px;} ";
  ptr +="h1 {margin: 50px auto 30px;} ";
  ptr +=".side-by-side{display: table-cell;vertical-align: middle;position: relative;}";
  ptr +=".text{font-weight: 600;font-size: 19px;width: 200px;}";
  ptr +=".reading{font-weight: 300;font-size: 50px;padding-right: 25px;}";
  ptr +=".temperature .reading{color: #F29C1F;}";
  ptr +=".humidity .reading{color: #3B97D3;}";
  ptr +=".pressure .reading{color: #26B99A;}";
  ptr +=".altitude .reading{color: #955BA5;}";
  ptr +=".superscript{font-size: 17px;font-weight: 600;position: absolute;top: 10px;}";
  ptr +=".data{padding: 10px;}";
  ptr +=".container{display: table;margin: 0 auto;}";
  ptr +=".icon{width:65px}";
  ptr +="</style>";
  ptr +="<script>\n";
  ptr +="setInterval(loadDoc,5000);\n";
  ptr +="function loadDoc() {\n";
  ptr +="var xhttp = new XMLHttpRequest();\n";
  ptr +="xhttp.onreadystatechange = function() {\n";
  ptr +="if (this.readyState == 4 && this.status == 200) {\n";
  ptr +="document.body.innerHTML =this.responseText}\n";
  ptr +="};\n";
  ptr +="xhttp.open(\"GET\", \"/\", true);\n";
  ptr +="xhttp.send();\n";
  ptr +="}\n";
  ptr +="</script>\n";
  ptr +="</head>";
  ptr +="<body>";
  ptr +="<h1>Car Port Weather Station</h1>";
  ptr +="<div class='container'>";
  ptr +="<div class='data temperature'>";
  ptr +="<div class='side-by-side icon'>";
  ptr +="<svg enable-background='new 0 0 19.438 54.003'height=54.003px id=Layer_1 version=1.1 viewBox='0 0 19.438 54.003'width=19.438px x=0px xml:space=preserve xmlns=http://www.w3.org/2000/svg xmlns:xlink=http://www.w3.org/1999/xlink y=0px><g><path d='M11.976,8.82v-2h4.084V6.063C16.06,2.715,13.345,0,9.996,0H9.313C5.965,0,3.252,2.715,3.252,6.063v30.982";
  ptr +="C1.261,38.825,0,41.403,0,44.286c0,5.367,4.351,9.718,9.719,9.718c5.368,0,9.719-4.351,9.719-9.718";
  ptr +="c0-2.943-1.312-5.574-3.378-7.355V18.436h-3.914v-2h3.914v-2.808h-4.084v-2h4.084V8.82H11.976z M15.302,44.833";
  ptr +="c0,3.083-2.5,5.583-5.583,5.583s-5.583-2.5-5.583-5.583c0-2.279,1.368-4.236,3.326-5.104V24.257C7.462,23.01,8.472,22,9.719,22";
  ptr +="s2.257,1.01,2.257,2.257V39.73C13.934,40.597,15.302,42.554,15.302,44.833z'fill=#F29C21 /></g></svg>";
  ptr +="</div>";
  ptr +="<div class='side-by-side text'>Temperature</div>";
  ptr +="<div class='side-by-side reading'>";
  ptr +=(int)temperature;
  ptr +="<span class='superscript'>&deg;C</span></div>";
  ptr +="</div>";
  ptr +="<div class='data humidity'>";
  ptr +="<div class='side-by-side icon'>";
  ptr +="<svg enable-background='new 0 0 29.235 40.64'height=40.64px id=Layer_1 version=1.1 viewBox='0 0 29.235 40.64'width=29.235px x=0px xml:space=preserve xmlns=http://www.w3.org/2000/svg xmlns:xlink=http://www.w3.org/1999/xlink y=0px><path d='M14.618,0C14.618,0,0,17.95,0,26.022C0,34.096,6.544,40.64,14.618,40.64s14.617-6.544,14.617-14.617";
  ptr +="C29.235,17.95,14.618,0,14.618,0z M13.667,37.135c-5.604,0-10.162-4.56-10.162-10.162c0-0.787,0.638-1.426,1.426-1.426";
  ptr +="c0.787,0,1.425,0.639,1.425,1.426c0,4.031,3.28,7.312,7.311,7.312c0.787,0,1.425,0.638,1.425,1.425";
  ptr +="C15.093,36.497,14.455,37.135,13.667,37.135z'fill=#3C97D3 /></svg>";
  ptr +="</div>";
  ptr +="<div class='side-by-side text'>Humidity</div>";
  ptr +="<div class='side-by-side reading'>";
  ptr +=(int)humidity;
  ptr +="<span class='superscript'>%</span></div>";
  ptr +="</div>";
  ptr +="<div class='data pressure'>";
  ptr +="<div class='side-by-side icon'>";
  ptr +="<svg enable-background='new 0 0 40.542 40.541'height=40.541px id=Layer_1 version=1.1 viewBox='0 0 40.542 40.541'width=40.542px x=0px xml:space=preserve xmlns=http://www.w3.org/2000/svg xmlns:xlink=http://www.w3.org/1999/xlink y=0px><g><path d='M34.313,20.271c0-0.552,0.447-1,1-1h5.178c-0.236-4.841-2.163-9.228-5.214-12.593l-3.425,3.424";
  ptr +="c-0.195,0.195-0.451,0.293-0.707,0.293s-0.512-0.098-0.707-0.293c-0.391-0.391-0.391-1.023,0-1.414l3.425-3.424";
  ptr +="c-3.375-3.059-7.776-4.987-12.634-5.215c0.015,0.067,0.041,0.13,0.041,0.202v4.687c0,0.552-0.447,1-1,1s-1-0.448-1-1V0.25";
  ptr +="c0-0.071,0.026-0.134,0.041-0.202C14.39,0.279,9.936,2.256,6.544,5.385l3.576,3.577c0.391,0.391,0.391,1.024,0,1.414";
  ptr +="c-0.195,0.195-0.451,0.293-0.707,0.293s-0.512-0.098-0.707-0.293L5.142,6.812c-2.98,3.348-4.858,7.682-5.092,12.459h4.804";
  ptr +="c0.552,0,1,0.448,1,1s-0.448,1-1,1H0.05c0.525,10.728,9.362,19.271,20.22,19.271c10.857,0,19.696-8.543,20.22-19.271h-5.178";
  ptr +="C34.76,21.271,34.313,20.823,34.313,20.271z M23.084,22.037c-0.559,1.561-2.274,2.372-3.833,1.814";
  ptr +="c-1.561-0.557-2.373-2.272-1.815-3.833c0.372-1.041,1.263-1.737,2.277-1.928L25.2,7.202L22.497,19.05";
  ptr +="C23.196,19.843,23.464,20.973,23.084,22.037z'fill=#26B999 /></g></svg>";
  ptr +="</div>";
  ptr +="<div class='side-by-side text'>Pressure</div>";
  ptr +="<div class='side-by-side reading'>";
  ptr +=(int)pressure;
  ptr +="<span class='superscript'>hPa</span></div>";
  ptr +="</div>";
  ptr +="<div class='data altitude'>";
  ptr +="<div class='side-by-side icon'>";
  ptr +="<svg enable-background='new 0 0 58.422 40.639'height=40.639px id=Layer_1 version=1.1 viewBox='0 0 58.422 40.639'width=58.422px x=0px xml:space=preserve xmlns=http://www.w3.org/2000/svg xmlns:xlink=http://www.w3.org/1999/xlink y=0px><g><path d='M58.203,37.754l0.007-0.004L42.09,9.935l-0.001,0.001c-0.356-0.543-0.969-0.902-1.667-0.902";
  ptr +="c-0.655,0-1.231,0.32-1.595,0.808l-0.011-0.007l-0.039,0.067c-0.021,0.03-0.035,0.063-0.054,0.094L22.78,37.692l0.008,0.004";
  ptr +="c-0.149,0.28-0.242,0.594-0.242,0.934c0,1.102,0.894,1.995,1.994,1.995v0.015h31.888c1.101,0,1.994-0.893,1.994-1.994";
  ptr +="C58.422,38.323,58.339,38.024,58.203,37.754z'fill=#955BA5 /><path d='M19.704,38.674l-0.013-0.004l13.544-23.522L25.13,1.156l-0.002,0.001C24.671,0.459,23.885,0,22.985,0";
  ptr +="c-0.84,0-1.582,0.41-2.051,1.038l-0.016-0.01L20.87,1.114c-0.025,0.039-0.046,0.082-0.068,0.124L0.299,36.851l0.013,0.004";
  ptr +="C0.117,37.215,0,37.62,0,38.059c0,1.412,1.147,2.565,2.565,2.565v0.015h16.989c-0.091-0.256-0.149-0.526-0.149-0.813";
  ptr +="C19.405,39.407,19.518,39.019,19.704,38.674z'fill=#955BA5 /></g></svg>";
 ptr +="</div>";
  ptr +="<div class='data altitude'>";
  ptr +="<div class='side-by-side icon'>";
  ptr +="<svg enable-background='new 0 0 58.422 40.639'height=40.639px id=Layer_1 version=1.1 viewBox='0 0 58.422 40.639'width=58.422px x=0px xml:space=preserve xmlns=http://www.w3.org/2000/svg xmlns:xlink=http://www.w3.org/1999/xlink y=0px><g><path d='M58.203,37.754l0.007-0.004L42.09,9.935l-0.001,0.001c-0.356-0.543-0.969-0.902-1.667-0.902";
  ptr +="c-0.655,0-1.231,0.32-1.595,0.808l-0.011-0.007l-0.039,0.067c-0.021,0.03-0.035,0.063-0.054,0.094L22.78,37.692l0.008,0.004";
  ptr +="c-0.149,0.28-0.242,0.594-0.242,0.934c0,1.102,0.894,1.995,1.994,1.995v0.015h31.888c1.101,0,1.994-0.893,1.994-1.994";
  ptr +="C58.422,38.323,58.339,38.024,58.203,37.754z'fill=#955BA5 /><path d='M19.704,38.674l-0.013-0.004l13.544-23.522L25.13,1.156l-0.002,0.001C24.671,0.459,23.885,0,22.985,0";
  ptr +="c-0.84,0-1.582,0.41-2.051,1.038l-0.016-0.01L20.87,1.114c-0.025,0.039-0.046,0.082-0.068,0.124L0.299,36.851l0.013,0.004";
  ptr +="C0.117,37.215,0,37.62,0,38.059c0,1.412,1.147,2.565,2.565,2.565v0.015h16.989c-0.091-0.256-0.149-0.526-0.149-0.813";
  ptr +="C19.405,39.407,19.518,39.019,19.704,38.674z'fill=#955BA5 /></g></svg>";
  ptr +="</div>";
  ptr +="<div class='side-by-side text'>Alt Estimated</div>";
  ptr +="<div class='side-by-side reading'>";
  ptr +=(int)altitude;
  ptr +="<span class='superscript'>m</span></div>";
  
  ptr +="</div>";
  ptr +="<div class='data altitude'>";
  ptr +="<div class='side-by-side icon'>";
  ptr +="<svg enable-background='new 0 0 58.422 40.639'height=40.639px id=Layer_1 version=1.1 viewBox='0 0 58.422 40.639'width=58.422px x=0px xml:space=preserve xmlns=http://www.w3.org/2000/svg xmlns:xlink=http://www.w3.org/1999/xlink y=0px><g><path d='M58.203,37.754l0.007-0.004L42.09,9.935l-0.001,0.001c-0.356-0.543-0.969-0.902-1.667-0.902";
  ptr +="c-0.655,0-1.231,0.32-1.595,0.808l-0.011-0.007l-0.039,0.067c-0.021,0.03-0.035,0.063-0.054,0.094L22.78,37.692l0.008,0.004";
  ptr +="c-0.149,0.28-0.242,0.594-0.242,0.934c0,1.102,0.894,1.995,1.994,1.995v0.015h31.888c1.101,0,1.994-0.893,1.994-1.994";
  ptr +="C58.422,38.323,58.339,38.024,58.203,37.754z'fill=#955BA5 /><path d='M19.704,38.674l-0.013-0.004l13.544-23.522L25.13,1.156l-0.002,0.001C24.671,0.459,23.885,0,22.985,0";
  ptr +="c-0.84,0-1.582,0.41-2.051,1.038l-0.016-0.01L20.87,1.114c-0.025,0.039-0.046,0.082-0.068,0.124L0.299,36.851l0.013,0.004";
  ptr +="C0.117,37.215,0,37.62,0,38.059c0,1.412,1.147,2.565,2.565,2.565v0.015h16.989c-0.091-0.256-0.149-0.526-0.149-0.813";
  ptr +="C19.405,39.407,19.518,39.019,19.704,38.674z'fill=#955BA5 /></g></svg>";
  ptr +="</div>";
  ptr +="<div class='side-by-side text'>Alt Actual</div>";
  ptr +="<div class='side-by-side reading'>";
  ptr +=(int)390; // insert actual Altitude above sea level 
  ptr +="<span class='superscript'>m</span></div>";
  ptr +="</div>";
  ptr +="</div>";
  ptr +="</body>";
  ptr +="</html>";
  return ptr;
}
