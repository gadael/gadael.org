---
title: Gadael.org
---

## Functionalities

Gadael is a powerful leave management application with rights attributions based on complex rules and periods planing. With Gadael you can:
 * Schedule multiples leave renewal periods in advance.
 * Automatically make adjustments of quantity based on the consumption on other rights.
 * Export your data for your payslip application.
 * Sync plannings with your google calendars.
 * Set up a policy for overtime recovery in the form of holiday entitlements.
 * Multi-level approval work-flow


![All devices compatible](images/devices.png)


## Install

### With git and NPM

Gadael can be installed from the git version, this is the best choice for taking advantage of the lastest features. For this to work, some dependencies need to be installed on the system first, you will have to install the dependencies using your system packages manager or manually.

* [nodejs](https://nodejs.org/)
* [MongoDB](https://www.mongodb.com/), the database server, version 3 is required.
* [git](https://git-scm.com/), this is the version control system used for Gadael development
* [npm](https://www.npmjs.com/), for nodejs dependencies
* [bower](https://bower.io/), for front-end dependencies

Then the install process can be resumed with this list of commands:

```bash
git clone https://github.com/gadael/gadael
cd gadael
npm install --production
bower install
```

Create the config file:

```bash
cp config.example.js config.js
```

You will have to configure an SMTP host and a root url in this file for the emails notifications to work correctly.

Database initialization:

```bash
node install.js gadael "Your company name" FR
```
First argument is the database name, default is gadael.
Second argument is your company name, default is "Gadael".
Third argument is the country code used to initialize the database, if not provided the leave rights list will be empty. You can check the list of [supported countries in the documentation](https://www.gadael.com/en/docs/version-master/008-the-countries.html)


You will have to start the server with this command:

```bash
node app.js
```

open http://localhost:3000 in your browser, you will be required to create an admin account on the first page.

![Success](images/success.jpg)

Congratulation! your setup is working!


### Using a Docker container

https://hub.docker.com/r/webinage/gadael


### Use the SaaS version

If you have no server available and want to take advantage of automatic updates to the last version, you can use Gadael directly from [gadael.com](https://www.gadael.com/), it's free for team up to 5 users. If you choose to subscribe to a paid plan you will benefit from the email technical support for all your configuration problems.


### Set up a reverse proxy

Application listen on localhost only by default, an https reverse proxy will be necessary to open access to users. This will add the ability to serve the application on the default port.

The examples given here are on a Debian system, a similar setup is possible with another distribution or package manager. We will use mydomain.com as an example URL, you must have a domain name already configured and linked to the web server by DNS configuration.


#### Using the Apache web server


So first of all, we need to install [Apache2](https://httpd.apache.org/):

```bash
apt update
apt install apache2
```

Next thing we'll have to do is proxy all request incoming on port 80 to the gadael local port. For this, we need to install/enable mod_proxy and mod_proxy_http modules on the Apache server:

```bash
a2enmod proxy
a2enmod proxy_http
```
Then we'll configure a VirtualHost like this:

```bash
vi /etc/apache2/sites-available/gadael.conf
```

With the content:

```
<VirtualHost *:80>
   ServerAdmin contact@mydomain.com
   ServerName mydomain.com

   ProxyRequests Off
   <Proxy *>
      Require all granted
   </Proxy>

   <Location /nodejsAppli>
      ProxyPass http://127.0.0.1:3000
      ProxyPassReverse http://1127.0.0.1:3000
   </Location>

   ErrorLog ${APACHE_LOG_DIR}/gadael-error.log
   CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

The new virtual host need to be enabled before restarting apache:

```
a2ensite mySite.conf
servicectrl restart apache2
```

After this last step, you can access your application using only http://mydomain.com/. This configuration can be enhanced by adding ssl certificates for the HTTPS protocol.

#### Using the nginx web server

Install [nginx](https://nginx.org/en/):

```bash
apt update
apt install nginx
```
Edit the default site configuration:

```bash
vi /etc/nginx/sites-available/default
```

Setting this content will redirect request from port 80 the the port 3000. If a port different than 3000 was used in your gadael configuration, you must change the `proxy_pass` line.

```
server {
    listen 80;
    server_name mydomain.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

A restart of the server is required:

```bash
systemctrl restart nginx
```

## Configure a custom login strategy

Login options are stored in the company document in database

Add a CAS authentication method with the command line mongo client :

```bash
$ mongo gadael
> var company = db.companies.find()[0];
> company.loginservices.cas = { "ssoBaseURL" : "https://....", "enable" : true }
> db.companies.update({ "_id": company._id }, company);
```

## Working with the API

gadael allows to use the API to exchange with the database in the same way as the web interface. To do this, you need to create a specific user with the administrator role.

![Creation link position in UI](images/create_api_access.png)

The obtained API key allow to authenticate with oauth2.

This is the list of paths with API access enabled by default:

api/admin/users
api/admin/usersstat
api/admin/accountrights
api/admin/accountcollections
api/admin/departments
api/admin/calendars
api/admin/calendarevents
api/admin/personalevents
api/admin/collaborators
api/admin/unavailableevents
api/admin/types
api/admin/specialrights
api/admin/rights
api/admin/rightrenewals
api/admin/accountbeneficiaries
api/admin/beneficiaries
api/admin/requests
api/admin/waitingrequests
api/admin/export
api/admin/consumption
api/admin/invitations

### Use the API to get the users list

step 1, get an access token:
```bash
$curl -X POST -d "grant_type=client_credentials&client_id=<your client ID>&client_secret=<your client secret>" https://demo.gadael.com/login/oauth-token

{"access_token":"55d99b2af2a2b2a914120e6e4b6e13f6bdbf88ed","token_type":"Bearer","expires_in":3599,"scope":[]}
```

step 2, use access token to get the list of users:
```bash
$curl -H "Authorization: Bearer 55d99b2af2a2b2a914120e6e4b6e13f6bdbf88ed" https://demo.gadael.com/api/admin/users

[...]
```
