[download]: https://github.com/ushahidi/platform-release/releases
[install-development]: https://github.com/ushahidi/platform/blob/develop/README.md#Installing-for-development-vagrant
[other-install-guides]: https://www.ushahidi.com/support/install-ushahidi
[docs]: https://www.ushahidi.com/support
[tech-docs]: ./docs/README.md
[getin]: https://www.ushahidi.com/support/get-involved
[issues]: https://github.com/ushahidi/platform/issues
[ush2]: https://github.com/ushahidi/Ushahidi_Web
[ushahidi]: http://ushahidi.com
[gitter]: https://gitter.im/ushahidi/Community

Ushahidi 3
============

[![Build Status](https://travis-ci.org/ushahidi/platform.png)](https://travis-ci.org/ushahidi/platform)
[![Stories up next](https://badge.waffle.io/ushahidi/platform.png?label=Stage:Backlog&title=Backlog)](https://waffle.io/ushahidi/platform)
[![Coverage Status](https://coveralls.io/repos/github/ushahidi/platform/badge.svg)](https://coveralls.io/github/ushahidi/platform)



[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy)

## What is Ushahidi?

Ushahidi is an open source web application for information collection, visualization and interactive mapping. It helps you to collect info from: SMS, Twitter, RSS feeds, Email. It helps you to process that information, categorize it, geo-locate it and publish it on a map.

## A note for grassroots organizations
If you are a grassroots organization, you can apply for a free social-impact responder account [here](https://www.ushahidi.com/plans/apply-for-free) after verifying that you meet the criteria.


## Getting Involved
There are many ways to get involved with Ushahidi, and some of them are great even for first time contributors. If you never contributed to Open Source Software before, or need more guidance doing it, please jump in our gitter channel with a clear description of what you are trying to do, and someone in there will try to help you.
These are some ways to get involved:

- **Documentation**: if you find an area of the Ushahidi platform that could use better docs, we would love to hear from you in an issue, and would be seriously excited if you send a [Pull Request](https://github.com/ushahidi/platform/compare). This is a great way to get involved even if you are not technical or just have a passion to make information more available and clear to everyone.
- **Report a bug**: oops. If you found an issue/bug, please be awesome and report it [here](https://github.com/ushahidi/platform/issues). Someone on the team will jump in to check it, try to help, and prioritize it for future development depending on the issue type.
- **Fix a bug**: everyone can use a hand from time to time. We are no different. If you want to help fixing a bug you or someone else found, we will be happy to review your PR and provide support.
- **Helping other users in the community**: you are welcome and encouraged to jump in and help other members of the community, either by responding to issues in github or jumping into our community channels to answer questions. 
- **New features**: our features are generally driven by our product and engineering team members, but if you have a great idea, or found a user need that we haven't covered, you are more than welcome to make a suggestion in the form of a github issue, or reach out to Ushahidi staff in [gitter]


## Using the Platform

- If you are not a developer, or just don't want to set it up yourself, you can start a hosted deployment [here](https://www.ushahidi.com/).



# Installing for development
## With Vagrant
### Installing the API

- Getting the API code
    - Clone the repository using `git clone https://github.com/ushahidi/platform.git` this will create a directory named _platform_ .
    - Go into the _platform_ directory (ie: `cd _platform_`)
    - Switch to the _develop_ branch (`git checkout develop`) 

- Once you have the code, the next step is to prepare a web server. We will use vagrant, with the Vagrant and Homestead.yml files that ship with Ushahidi.

    Prerequisites: 
    - [Vagrant](https://www.vagrantup.com/downloads.html)
    - Recommended: [Vagrant host-updater plugin](https://github.com/cogitatio/vagrant-hostsupdater) - this is useful to avoid having to update /etc/hosts by hand
    - [VirtualBox](https://www.virtualbox.org/wiki/Downloads) - Note: Windows users may be required to Enable VT-X (Intel Virtualization Technology) in the computer's bios settings, disable Hyper-V on program and features page in the control panel, and install the VirtualBox Extension Pack (installation instructions here.)
    - [Composer](https://getcomposer.org/doc/00-intro.md#system-requirements)
    - PHP >= 5.6

#### Installation steps
First up we need to install the PHP dependencies

- In the _plaform_ directory, run  `composer install --ignore-platform-reqs`. 

Note: Without using --ignore-platform-reqs you might run into an error like "The requested PHP extension ... is missing from your system". You generally won't need all the PHP extensions on your _host_ machine, since the vagrant setup already has them.

If you get a warning like "In MemcachedConnector.php line 69:  Class 'Memcached' not found" at this point you can safely ignore it, we will come back to it later.


- Bring up the vagrant server. Since this is the first time you run it, it will also provision the machine from scratch:

   `vagrant up`

Our vagrant box is built on top of Laravel's Homestead, a pre-packaged Vagrant box that provides a pre-built development environment. Homestead includes the Nginx web server, PHP 7.1, MySQL, Postgres, Redis, Memcached, Node, and all of the other goodies you might need.

If you see an error like "Vagrant was unable to mount VirtualBox shared folders...", try upgrading VirtualBox or edit Homestead.yaml and change the folders to NFS as shown below, then re-run "vagrant" up.

      -
          map: "./"
          to: /vagrant
          type: "nfs"
      -
          map: "./"
          to: /home/vagrant/Code/platform-api
          type: "nfs"
  - You will have to ssh into your vagrant machine to finish installing the dependencies if you used --ignore-platform-reqs before

  `vagrant ssh`
  `cd ~/Code/platform-api`
  `	sudo update-alternatives --set php /usr/bin/php7.1`
  `composer install` => if this fails you can still run it --ignore-platform-reqs 

- Important: If you didn't setup vagrant-hostupdater, you will need to add the following lines to /etc/hosts:
```
192.168.33.110  platform-api
192.168.33.110  api.ushahidi.test
```

At this point you should have a running web server, but your deployment isn't set up yet. We still need to configure the database and run the migrations.


##### Setting up the deployment's database
- Copy the configuration file `.env.example` to make sure the platform can connect to the database.

    `cp .env.example .env`

- Run the migrations. This is required to be able to use your deployment, since it includes basic data such as an initial "admin" user, roles, the database schema itself, etc.

    `composer migrate`

- Go to http://192.168.33.110 in your browser to check the API is up and running. You should see some JSON with an API version, endpoints and user info. 

## With XAMPP
- Install XAMPP
- Verify that Mysql and Apache are running in the xampp control panel
- Go to http://localhost and verify that you see the XAMPP dashboard page
- In the XAMPP control panel, click on the button "Admin" for MySQl
	- in the mysql dashboard, create a new database called `platform`. 
- In the XAMPP control panel, click on "Shell" 
- Run `cd htdocs`
- Run `git clone git@github.com:ushahidi/platform.git platform` to clone the platform code inside your htdocs directory
- `cd platform` 
- Install composer following the instructions for your environment [here](https://getcomposer.org/doc/00-intro.md)
- Copy `.env.example` to a new file called `.env` to create the default .env file
- open .env with your IDE or text editor. 
    - Change the CACHE_DRIVER to be file instead of memcache (you can also set it up with memcache, but for simplicity we 
    - Change the DATABASE HOST to 127.0.0.1
    - Change the DATABASE USER to your mysql user  (root in the default XAMPP install)
    - Change the DB PASSWORD  (empty by default with XAMPP)
	- Change the DB name to `platform`
- A note on composer: if you didn't setup composer globally, you should use `php composer.phar {command}` instead of `composer {command}` in the next two steps 
    - run `composer install`. Wait while composer installs all the dependencies
    - run `composer migrate` to run the database migrations. This will create all the necessary tables and a default `admin` user with password `administrator`
- at this point you have the API ready to run, but need to setup some apache rules to be able to access it correctly.
- Add the api url to your hosts file (127.0.0.1 api.ushahidi.test)
- Add this file platform/httpdocs/.htaccess:
```
# Turn on URL rewriting
RewriteEngine On

# Set base directory
RewriteBase /httpdocs
RewriteCond %{HTTP:Authorization} .
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

# Protect hidden files from being viewed
<Files .*>
Order Deny,Allow
Deny From All
</Files>

# Uncomment to force redirection to https site.
#RewriteCond %{HTTP:X-Forwarded-Proto} =http
#RewriteRule ^(.*)$ https://%{HTTP_HOST}%{ENV:REWRITEBASE}$1 [R=301,L]

# Allow any files or directories that exist to be displayed directly
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d

# Rewrite all other URLs to index.php/URL
RewriteRule .* index.php/$0 [PT]
```
- Add this file platform/.htaccess
```
# Turn on URL rewriting
RewriteEngine On

# Set base directory
#RewriteBase /platform

# Protect hidden files from being viewed
<Files .*>
Order Deny,Allow
Deny From All
</Files>

# Rewrite all other URLs to httpdocs
RewriteRule .* httpdocs/$0 [PT]

```
- In your httpd.conf file (open xampp => config -> httpd.conf) , add this virtualhost
```
<VirtualHost *:80>
ServerAdmin webmaster@localhost
DocumentRoot "C:/newxamp/htdocs/platform"
ServerName ushahidi.api.test
<Directory "C:/newxamp/htdocs">
AllowOverride all
</Directory>
</VirtualHost>
```

- You're all done. You should be able to access api.ushahidi.test now and see the default API response


# Installing the client

- The latest install instructions for the client are always [in the platform-client README, at this url](https://github.com/ushahidi/platform-client/blob/develop/README.md). 

## Useful Links

- [Download][download]
- [Other Installation Guides][other-install-guides]
- [User Documentation][docs]
- [Technical Documentation][tech-docs]
- [Get Involved][getin]
- [Bug tracker][issues]
- [Ushahidi][ushahidi]
- [Ushahidi Platform v2][ush2]
