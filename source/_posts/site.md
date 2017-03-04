---
title: Gadael.org
---

## Functionalities

Gadael is a powerfull leave management application with rights attributions besed on complex rules and periods planification. With Gadael you can:
 * Schedule multiples leave renewal periods in advance.
 * Automatically make adjustments of quantity based on the consuption on other rights.
 * Export your data for your payslip application.
 * Sync plannings with your google calendars.
 * Set up a policy for overtime recovery in the form of holiday entitlements.
 * Multi-level approval workflow

## Install

### On a Debian System

The gadael debian package will be available soon!

### Using a Docker container

The Gadael docker file will be available soon!

### With git and NPM

Gadael can be installed from the git version, this is the best choice for taking advantage of the lastest features. For this to work, some dependencies need to be installed on the system first, you will have to install the dependencies unsing your system packages manager or manually.

* [nodejs](https://nodejs.org/)
* [MongoDB](https://www.mongodb.com/), the database server.
* [git](https://git-scm.com/), this is the version control system used for Gadael development
* [npm](https://www.npmjs.com/), for nodejs dependencies
* [bower](https://bower.io/), for frontend dependencies

Then the install process can be resumed with this list of commands:

```bash
git clone https://github.com/gadael/gadael
cd gadael
npm install
bower install
```

Create the config file:

```bash
cp config.example.js config.js
```

You will have to configure an SMTP host and a root url in this file for the emails notifications to work correctly.

After a database initialisation like explained in the "Using Gadael" chapter, you will have to start the server manually with this command:

```bash
node app.js
```


### Use the SaaS version

If you have no server available and want to take advantage of automatic updates to the last version, you can use Gadael directly from [gadael.com](https://www.gadael.com/), it's free for team up to 5 users. If you choose to suscribe to a paid plan you will benefit from the email technical support for all your configuration problems.

## Using Gadael



A script in provided to initialize the database:

```bash
node install.js gadael "Your company name" FR
```
First argument is the database name, default is gadael.
Second argument is your company name, default is "Gadael".
Third argument is the country code used to initialize the database, if not provided the leave rights list will be empty. You can check the list of [supported countries in the documentation](https://www.gadael.com/en/docs/version-master/008-the-countries.html)


open http://localhost:3000 in your browser, you will be required to create an admin account on the first page.

Congratulation! your setup is working!

![Success](images/success.jpg)

### Set up a reverse proxy

Application listen on localhost only by default, an https reverse proxy will be necessary to open access to users. This will add the ability to serve the application on the default port.

#### Using the Apache web server


#### Using the nginx web server
