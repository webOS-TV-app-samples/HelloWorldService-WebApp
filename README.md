# Hello World Service (Web App)

## About Code Sample

In this sample, we will create a simple HelloWorld app that demonstrates how to create a webOS TV service. This sample also demonstrates how to call the service from any web app and display the response. We will be using the command line tool (CLI) of the webOS TV SDK to create the app and service, as well as packaging and installing the app on the target device.

For more information about the webOS TV CLI and its commands written on this page, see [Command Line Interface](https://webostv.developer.lge.com/sdk/command-line-interface/).

## Creating a Project

To start, we create a new app project using the following command.

```powershell
ares-generate -t basic app
? app id com.yourdomain.helloworld
? title Hello World
? version 0.0.1
```

Using the **-t** option, we specified to use the basic template. Also, we used "app" for the app directory name for which any name can be used.

Note that the app ID MUST be unique, formed from reverse DNS naming conventions with the following rules.

- Must create the ID only with lowercase letters (a-z), digits (0-9), minus signs, and periods. It must be at least two characters long and must start with an alphanumeric character.
- Cannot start with the following reserved domain names: com.palm, com.webos, com.lge, com.palmdts

## Writing an App

We now have a skeleton project on which to build the HelloWorld app and service. Before you start writing an app, open appinfo.json and specify the app name at "id:" field. In this code sample, I set it to **com.yourdomain.helloworld**.

First, we create an app that calls the service. Using your favorite editor, open **index.html,**and replace the code with the code below.

```html
<!DOCTYPE html>
<html>
  <head>
    <style type="text/css">
              body {
                  width: 100%;
                  height: 100%;
                  background-color: #202020;
              }       
              * {
                  margin-top: 100px;
                  vertical-align: middle;
                  text-align: center;
                  color: #FFFFFF;
                  font-size: 40px;
              }       
              h1 {
                  color: #a0a0a0;
              }       
              .btn {
                  color: #000000;
              }
    </style>
    <script type="text/javascript" src="webOSTVjs-1.2.3/webOSTV.js"></script>
    <script type="text/javascript" src="js/script.js"></script>
  </head>
  <body>
    <div>
      <h1>JS SERVICE SAMPLE</h1>
      <input
        type="button"
        value="CALL SERVICE"
        onclick="displayReponse()"
        class="btn"
      />
      <input type="text" id="input" class="btn" />        
      <p id="result1"></p>
      <p id="result2"></p>
    </div>
  </body>
</html>
```

The webOSTV.js library is basically included in the basic template of the CLI. However, we recommend that you download and use the latest version of the library. You can download the latest webOSTV.js library on the [webOSTV.js Introduction](https://webostv.developer.lge.com/api/webostvjs/intro-webostvjs/) page.

In the application source file, **js/script.js**, insert the following code which uses webOS.service.request to call service. Also, add the functions for displaying the response from the service.

```javascript
function displayReponse() {
  const value = document.getElementById('input').value;
  webOS.service.request('luna://com.yourdomain.helloworld.service/', {
    method: 'hello',
    parameters: { name: value },
    onFailure: showFailure,
    onSuccess: showSuccess,
  });
}
function showSuccess(res) {
  document.getElementById('result1').innerHTML = 'Service Responded!';
  document.getElementById('result2').innerHTML = res.data;
}
function showFailure(err) {
  document.getElementById('result1').innerHTML = 'Failed!';
  document.getElementById('result2').innerHTML = err.toString();
}
```

## Writing a Service

Now create a service to the app in a separate directory using the following command.

```powershell
ares-generate -t js_service service
? service id com.yourdomain.helloworld.service
```

Using the **-t** option, we specified to use the js_service template. Also, we used "service" for the service directory name for which any name can be used.

Note that the service ID must be a subdomain of the app ID to which the service belongs. For more information, see [Creating JS Service](https://webostv.developer.lge.com/develop/js-services/creating-js-services/).

To register the JS service, services.json and package.json files are required. However, if you create a service using the ares-generate command, the files are automatically generated.

Using your favorite editor, open helloworld_service.js and replace the code with the code below.

```javascript
var pkgInfo = require('./package.json');
var Service = require('webos-service');
var service = new Service(pkgInfo.name);
var name = 'World';
service.register('hello', function (message) {
  console.log('In hello callback');
  if (message.payload && message.payload.name) {
    name = message.payload.name;
  }
  message.respond({
    returnValue: true,
    data: 'Hello, ' + name + '!',
  });
});
```

## Packaging the App

We are now ready to package our app and test it on the target device. Create the package using the following command.

<mark>ares-package app service</mark>

## Installing and Launching the App

Before installing the packaged app, you should run the webOS TV emulator first. Then, install the app on the target device using the ares-install command.

<mark>ares-install com.yourdomain.helloworld_0.0.1_all.ipk</mark>

After you install the app, you can launch the app by selecting the app icon on the Launcher or executing the following command.

<mark>ares-launch com.yourdomain.helloworld</mark>

After the app is running, enter your name in the text box, then click the **CALL SERVICE** button. The app will call the service with the name entered as a parameter and return the "Hello" response.

![The result image of this code sample](https://webostv.developer.lge.com/download_file/view_inline/2124/)

## Do's and Don'ts

- **DO** match the base app ID with the service ID as in this example (com.yourdomain.helloworld).

- **DON'T** expect this to work in a browser.
