# Acme Control Panel Example

This SmartApp hosts a web page dashboard that allows the control of scenes, locks, and switches. 
It also creates two devices in the SmartThings location, which are also reflected on
the dashboard. It's an example of how to:
* Execute scenes
* Execute device commands
* Subscribe to device state change events by capability
* Store the auth tokens and settings of installed app instances and use them when making 
  calls to the SmartThings API that are not in response to SmartApp lifecycle events.
* Create devices
* Respond to device commands

> **Info:** For an AWS lambda example (which includes adding cron to auto run smartapp) using this [repo](https://github.com/SmartThingsCommunity/smartapp-example-no-devices-nodejs-lambda)

## File Structure

* lib
  * db.js &mdash; module for listing installed app instances from the context store
  * smartapp.js &mdash; the SmartApp implementation
  * sse.js &mdash; Server-Sent Event object used to update the web page  
* locales
  * en.json &mdash; English version of the app configuration page text
* public
  * images &mdash; web image files
  * javascript &mdash; web page JavaScript files
  * stylesheets &mdash; web page css files
* routes
  * index.js -- defines routes for the dashboard and SmartApp endpoint
  * oauth.js - defines routes for the OAuth server (not normally part of a SmartApp)
* server.js &mdash; the Express server that hosts the SmartApp as a web-hook
* views
  * index.ejs &mdash; the home page
  * dashboard.ejs &mdash; the installed app instance control page
  * login.js &mdash the OAuth login page

## Prerequisites
- A [Samsung Developer Workspace account](https://smartthings.developer.samsung.com/workspace/)

- [Node.js](https://nodejs.org/en/) and [npm](https://www.npmjs.com/) installed

- [ngrok](https://ngrok.com/) or similar tool to create a secure tunnel to a publicly available URL
  
## Getting Started

### Clone this GitHub repository
```bash
git clone git@github.com:PassivUK/smartthings-acme-control-panel-example.git
```

### Create the local data directory
```bash
cd device-scene-example-nodejs
mkdir data
mkdir data/smartthings
mkdir data/acme
```

### Start the server
```bash
npm install
node server.js
```

### Start ngrok and point it to your server
```
ngrok http localhost:3001
```
Make note of the HTTPS forwarding URL, for example `https://c79461932dfc.ngrok.io`

### Create an automation in the developer workspace

Go to the SmartThings developer workspace and create an Automation SmartApp. 
This app should have the scopes:
```
r:devices:*
x:devices:*
r:scenes:*
x:scenes:*
w:devices:*
i:deviceprofiles:*
```
Choose the web-hook option when creating the app. The _targetURL_ should be set to the ngrok forwarding
URL with the path `/smartapp`, for example `https://c79461932dfc.ngrok.io/smartapp`

Take note of the `appId`, `clientId`, and `clientSecret` displayed after creating your app.

### Confirm that your app is ready to receive lifecycle events

Look in your server log for a line with a URL to use for verifying your app. Visit this URL to confirm
the location of your app (you can do this in a web browser).

### Create a .env file and restart the server

Create a file named `.env` in the project directory that sets your `appId`, `clientId`, and `clientSecret`.
For example:
```
ACME_SERVER_URL=https://smartthings.ngrok.io   # This is the ngrok URL given
ACME_CLIENT_ID=20406d6c-eaec-4e2e-bc65-63be82da57c0   #leave as is
ACME_CLIENT_SECRET=40d4362b-8405-43bd-8a15-73cfcf12d550 #leave as it

ST_APP_ID=a177b816-7775-45e7-b1a7-98690b8a8031  # Smartthings APP ID
ST_CLIENT_ID=f7f6de29-9c8e-4cab-8f81-274afd12a692 # Smartthings Client Id
ST_CLIENT_SECRET=1b21c2d4-40db-414e-ac1b-d9b5891d1c4f # Smartthings Secret

DEVICE_PROFILE_ID=85cdaa4a-ebc7-4459-9845-880ad34ae015  # Can be retrieved using smartthings deviceprofile. This one is the Passiv_Acme_Test
```
and restart your NodeJS server. Do not restart ngrok or the URL will change (unless your are using a 
paid account)

> **Note:** A new DEVICE_PROFILE_ID can be created using _smartthings deviceprofile:create_. It must have switch capabilities for the acme devices


### Install your SmartApp and visit the web page

Put the app into developer mode:
1. Go to the smartthings dev workspace and select your smart app.
2. In the lefthand pane select Test -> Developer Mode
3. Deploy to Test

Install your SmartApp using the SmartThings mobile app:
1. Open smartthings app on device
2. Select Automations
3. Click the compass icon in top right.
4. Scroll down and select your Smart App
5. Give requested access

> **Note:** If no SmartApps appear, [try enabling developer options and retrying](https://developer.smartthings.com/docs/devices/direct-connected/test-your-device-app/#enable-developer-mode-in-the-smartthings-app)

Then visit your local web server to see and control devices and scenes:

http://localhost:3001

When you have selected the Installed App Instance you should see similar to the following:

![Example Working App Screenshot](./public/images/LocalServerScreen.png?raw=true)
