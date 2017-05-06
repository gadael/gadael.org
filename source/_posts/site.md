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


### On Ubuntu server with the PPA

You can configure a PPA server on your system to receive regular  packages updates.

Add the PPA:

```bash
sudo add-apt-repository ppa:gadael/master
sudo apt update
```

Install Gadael:

```bash
sudo apt install gadael
```


### On a Debian or ubuntu system


The deb package can be downloaded from the release page on the github projet:
https://github.com/gadael/gadael/releases

```bash
apt install nodejs mongodb
dpkg -i gadael*.x86_64.deb
```

The gadael http server can be started with:

```bash
systemctl start gadael
```

### For CentOS, Fedora or redhat

start by installing nodejs, for this an additional repository is needed:

```bash
curl --silent --location https://rpm.nodesource.com/setup_7.x | bash -
```

There is no mongodb package for redhat, you can add a repository for the mongodb-org package as explained on [the mongodb website](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/).

The rpm package can be downloaded from the release page on the github projet:
https://github.com/gadael/gadael/releases

The following command will install the rpm file and the dependencies:

```bash
yum --nogpgcheck localinstall gadael-*.x86_64.rpm
```

The gadael http server can be started with:

```bash
systemctl start gadael
```


### Using a Docker container

The Gadael docker file will be available soon!

### With git and NPM

Gadael can be installed from the git version, this is the best choice for taking advantage of the lastest features. For this to work, some dependencies need to be installed on the system first, you will have to install the dependencies using your system packages manager or manually.

* [nodejs](https://nodejs.org/)
* [MongoDB](https://www.mongodb.com/), the database server.
* [git](https://git-scm.com/), this is the version control system used for Gadael development
* [npm](https://www.npmjs.com/), for nodejs dependencies
* [bower](https://bower.io/), for front-end dependencies

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

After a database initialization like explained in the "Using Gadael" chapter, you will have to start the server manually with this command:

```bash
node app.js
```


### Use the SaaS version

If you have no server available and want to take advantage of automatic updates to the last version, you can use Gadael directly from [gadael.com](https://www.gadael.com/), it's free for team up to 5 users. If you choose to subscribe to a paid plan you will benefit from the email technical support for all your configuration problems.

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
