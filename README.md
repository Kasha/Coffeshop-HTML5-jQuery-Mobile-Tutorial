# How to make an application for Coffeeshop framework using jQuerymobile, HTML5, PHP and Osgibroker

## Step 1: Creating a HTML5 page with basic jquery functionalities

In this tutorial I start by creating directory called large. This directory will contain files that are required for the Coffeshop applicaiton.

In this step I start by creating a simple HTML page with basic HTML elements of HTML, HEAD and BODY. The goal of this step is to have enable a fucntionality for user to be able to change the background color of the Body element.

For this I created the index.html page inside the large folder. The code for this step is as follow:

```
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
		  $("body").css("background-color","purple");
		  break;
		}
	}
  </script>
  </head>
  <body>
  </body>
</html>
```

