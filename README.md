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
## Step 2: User Osgibroker to interact with the Application HTML page

### Objectives: 
1. Use ogibroker interface to interact with the application
2. Trigger an event on the application using the Ogibroker interface



