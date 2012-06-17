# How to make an application for Coffeeshop framework using jQuerymobile, HTML5, PHP and Osgibroker

Each step of this tutorial is separated in different directory.

## Step 1: Creating a HTML5 page with basic jquery functionalities

### Objective:
1. create a simple HTML5 page and enable users to change the background color of the Body element by calling a javascript function

I start by creating a simple HTML page with basic HTML elements of HTML, HEAD and BODY. I created the index.html page inside the large folder. The code for this step is as follow:

```html
<!DOCTYPE html>
  <head>
    <script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>
    <script>
	function changeBGColor(colorname){
		switch(colorname)
		{
		case "black":
		  $("body").css("background-color","black");
		  break;
		case "red":
		  $("body").css("background-color","red");
		  break;
		case "yellow":
		  $("body").css("background-color","yellow");
		  break;
		default:
		  $("body").css("background-color",colorname);
		  break;
		}
	}
  </script>
  </head>
  <body>
  </body>
</html>
```

Here as you can see in the code, line 3 loads the jQuery javascript library into the page and below that is a simple funciton that accepts the colorname parameter and changes the body tag backgroud-color using a switch statement.

To interact with the page you can simply use the develeoper tools of the chrome browser. Open index.html using chrome and open the developer tools by tools -> developer tools (CTRL+SHIFT+J). In the console type the following;

```javascript
changeBGColor("red");
```
You can see that the background color of the page changes based on the input of the changeBGColor.
## Step 2: Use Osgibroker to interact with the HTML page

### Objectives: 
1. Use ogibroker interface to interact with the application
2. Trigger an event on the Tutorial application using the Ogibroker interface

The interact with the application we use the OSGiBroker. OSGiBroker enables developers to easily implment communication between two applications using their REST API. To read more about OSGiBroker on their website [OSGiBroker](http://www.magic.ubc.ca/wiki/pmwiki.php/OSGiBroker/OSGiBrokerOverview).

The OSGiBroker has to be installed on the system, you can read more about how to install the OSGiBroker on ( [installation guid](http://www.magic.ubc.ca/wiki/pmwiki.php/OSGiBroker/Installation) ).

To implement the communication between our applciation and another application we need to use the OSGiBroker REST API. There is a complete instruction on how to use this API found [here](http://magic.ubc.ca/wiki/pmwiki.php/OSGiBroker/Osgibroker-rest). 

First the application has to be subscribed on the OSGiBroker server on the machine. We can directly go on the link bellow to subcribe to our tutorial application:

```
http://localhost:8800/osgibroker/subscribe?topic=tutorial&clientID=tutorial
```  
The two parameters topic and clientID defines the application name and client who used the application. These two parameters have to be sent to http://localhost:8800/osgibroker/subscribe to properly register the application. We can automatically subscribe to the application everytime the page loads by asynchrnously loading this URL in our tutorial application using an ajax call.

Next, we need to see how we can trigger an event on the OSGiBRoker. For that we can use the URL below:

```
http://localhost:8800/osgibroker/event?topic=tutorial&clientID=tutorial&_method=POST&eventName=blue
```
In this url we can trigger an event for the tutorial application on the OSGiBroker by assigning the event's name to the eventName parameter in the URL above. When this URL is requested from the OSGiBroker server the following response is sent to the browser indicating that the event is successfully trigered for the tutorial application.

```xml
<event timestamp="1339967009799">
<topic>tutorial</topic>
<clientID>tutorial</clientID>
<eventName>blue</eventName>
<_method>POST</_method>
</event>
``` 

Once this event is triggered, it remains on the server for the tutorial applicatin, until the event is retrieved from the application using the URL below:

```
http://localhost:8800/osgibroker/event?topic=tutorial&clientID=tutorial&timeOut=1
```

And the response is:

```xml
<events>
<event timestamp="1339970751526">
<topic>tutorial</topic>
<clientID>tutorial</clientID>
<eventName>blue</eventName>
<_method>POST</_method>
</event>
</events>
```
We can get the triggered event from the OSGiBroker server and handle it on the application.

Now we have figured out how the OSGiBroker works and how to subscribe the application and triger an even. Next we are going to implement subscribing the Tutorial application on the OSGiBroker and handling events from OSGiBroker on our application.

For that we need two separate php files: subscribe.php and event.php. Codes for the two files are provided below:

subscribe.php
```php
<?php

$daurl = 'http://localhost:8800/osgibroker/subscribe?topic=tutorial&clientID=tutorial';

// Get that website's content
$handle = fopen($daurl, "r");

// If there is something, read and return
if ($handle) {
    while (!feof($handle)) {
        $buffer = fgets($handle, 4096);
        echo $buffer;
    }
    fclose($handle);
}
?>
```

event.php
```php
<?php

$daurl = 'http://localhost:8800/osgibroker/event?topic=tutorial&clientID=tutorial&timeOut=1';

// Get that website's content
$handle = fopen($daurl, "r");

// If there is something, read and return
if ($handle) {
    while (!feof($handle)) {
        $buffer = fgets($handle, 4096);
        echo $buffer;
    }
    fclose($handle);
}
?>
```
Now we need to modify the index.html

```html
<!DOCTYPE html>
  <head>
    <script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>
    <script>
	$(document).ready(function(){
		//subscribe the tutorial application to the tutorial topic
		$.ajax({ type: "GET", url: "subscribe.php", dataType: "xml", success: messageParser });

		//retreive events and parse them.
		setInterval(function () {
			$.ajax({ type: "GET", url: "event.php", dataType: "xml", success: messageParser });		
			}, 2000);
	});

	//using this parser we will get the events and add them to the DOM
	function messageParser(xml) {
		$(xml).find("event").each(function () {
			var color = $(this).find("eventName").text();
			changeBGColor(color);
		});
	}
	
	function changeBGColor(colorname){
		switch(colorname)
		{
		case "black":
		  $("body").css("background-color","black");
		  break;
		case "red":
		  $("body").css("background-color","red");
		  break;
		case "yellow":
		  $("body").css("background-color","yellow");
		  break;
		default:
		  $("body").css("background-color",colorname);
		  break;
		}
	}
  </script>
  </head>
  <body>
  </body>
</html>
```
Line 7 uses the ajax function of the jquery library to send an asynchronous request to subscribe.php. In line 10, we set an interval to send a request to OSGiBroker looking for any available event. If there is any event in the response of this request, then it can get handled using the messageParse.

We can try this code by using the URL below:

```
http://localhost:8800/osgibroker/event?topic=tutorial&clientID=tutorial&_method=POST&eventName=blue
```
you can change the eventName parameter to any other color and you can see how the application handles these different events.

## Step 3: Create the Jquery Mobile interface

For this tutorial, we use [jQuery mobile](http://jquerymobile.com) to build an interface to interact with our application. For that, first we need to creat an applicatin interface using the jquerymobile library. Luckiliy jQuery mobile has an interactive WYSIWG editor to create such an interface available on their website. 

![jQuery mobile editor](https://github.com/hmrtk/Coffeshop-HTML5-jQuery-Mobile-Tutorial/blob/master/images/2.PNG?raw=true)

I used this editor and generated an interface for our application:

![Tutorial app jquery mobile interface](https://github.com/hmrtk/Coffeshop-HTML5-jQuery-Mobile-Tutorial/blob/master/images/1.PNG?raw=true)

mobile/index.html

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <title>
        </title>
        <link rel="stylesheet" href="http://code.jquery.com/mobile/1.1.0/jquery.mobile-1.1.0.min.css" />
        <link rel="stylesheet" href="my.css" />
        <style>
            /* App custom styles */
        </style>
        <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js">
        </script>
        <script src="http://code.jquery.com/mobile/1.1.0/jquery.mobile-1.1.0.min.js">
        </script>
    </head>
    <body>
        <div data-role="page" id="page1">
            <div data-theme="b" data-role="header" data-position="fixed">
                <h3>
                    Tutorial Sample App
                </h3>
            </div>
            <div data-role="content" style="padding: 15px">
                <a id="test1" class="appbutton" data-role="button" data-ajax="false" data-transition="fade" data-theme="e" value="Yellow">
                    Yellow
                </a>
                <a class="appbutton" data-role="button" data-transition="fade" data-theme="b" value="Blue">
                    Blue
                </a>
                <a class="appbutton" data-role="button" data-transition="fade" data-theme="a" value="Black">
                    Black
                </a>
		<div id="resultLog"></div>
            </div>
        </div>
        <script>
        $(function() {
 
            $(".appbutton").click(function() {

		     $.ajax({
		      type: "GET",
		      url: "event.php",
		      data: ({event: $(this).attr('value')}),
		      cache: false,
		      dataType: "XML",
		      success: onSuccess
		    });

            }); 
            function onSuccess(data)
            {
                $("#resultLog").html("Command Sent: " + data);
            }
        });
        </script>
    </body>
</html>
```

What this code does is that it creates three buttons on the screen. A color name is assigned to the value attribute of each buttons elements. In the jquery function, once the page is loaded a request is sent to event.php page sending this color value as parameter. The event.php page simply triggers gets the color name by get method and sends a request to OSGiBroker server for triggering an even with the color name on the application.

event.php

```php
<?php
// Set your return content type
//header('Content-type: application/xml');

$daurl = "http://localhost:8800/osgibroker/event?topic=tutorial&clientID=tutorial&_method=POST&eventName=".$_GET['event'];
echo $daurl;

// Get that website's content
$handle = fopen($daurl, "r");

// If there is something, read and return
if ($handle) {
    while (!feof($handle)) {
        $buffer = fgets($handle, 4096);
        echo $buffer;
    }
    fclose($handle);
}
?>
```
## Step 4: Setup application on the coffeshop framework

To setup the application on the coffeeshop framework, we need to create a new application on the coffeshop framework mainpage.

![Creating a new application of Coffeeshop](https://github.com/hmrtk/Coffeshop-HTML5-jQuery-Mobile-Tutorial/blob/master/images/3.PNG?raw=true)


Then we need to fillup the form for creating the new application:

![New application settings](https://github.com/hmrtk/Coffeshop-HTML5-jQuery-Mobile-Tutorial/blob/master/images/4.PNG?raw=true)


