---
layout: post
title: Setup Craft Locally
---

Configure a user-friendly name (e.g. local.example.dev) in the local `/private/etc/hosts` file to point to local environment. And, optionally, configure a name (e.g. stage.example.dev, example.com) to point to the server.

File: /private/etc/hosts

	127.0.0.1 localhost
	127.0.0.1 example.dev
	127.0.0.1 local.example.dev


### Config Virtual Hosts in Apache (MAMP).
Now that `local.example.dev` points to the local server, we need to map configure Apache to respond to the request. 

`/Applications/MAMP/conf/apache/extra/httpd-vhosts.conf`

    <VirtualHost *:80>
        DocumentRoot "/Users/Chris/Code/example.com/public"
        ServerName example.dev
    </VirtualHost>

Point your browser towards `http://local.example.dev` to test the config.

### Create Databases with Users and Passwords
Create a database (e.g. craftDB) using phpMyAdmin

### Config db.php (Simple Version)
Configure `/craft/config/db.php` to use the new database.

    return array(
    
        // The database server name or IP address. Usually this is 'localhost' or '127.0.0.1'.
        'server' => 'localhost',
    
        // The database username to connect with.
        'user' => 'root',
    
        // The database password to connect with.
        'password' => 'root',
    
        // The name of the database to select.
        'database' => 'exampleDev',
    
        // The prefix to use when naming tables. This can be no more than 5 characters.
        'tablePrefix' => 'craft',
    
    );

### Config db.php (Multi-Environment)
The basic setup shown above is easy but it has 2 drawbacks. First, it can make deploying a bit of a hassle since you need to reconfigure the db.php for each environment. Second, the database passwords are stored openly. Both issues are worse when the db.php is added to source control. So here's a better way...

Configure the db.php for multiple environments. And move the sensitive database info out of db.php to the `httpd-vhosts.conf` file and access them using Environment Variables.

First, we'll use SetEnv to create Environment Variables for sensitive info. Add the following to the `httpd-vhosts.conf` file.


    <VirtualHost *:80>
        DocumentRoot "/Users/Chris/Code/example.com/public"
        ServerName example.dev
    
        # Use environment variables to (more) securely store passwords 
        # See Multi-Environment configuration (See craft/config/db.php)
        # Craft database, user and password
        SetEnv mysql.user "root"
        SetEnv mysql.password "Password-Phrase!!"
        SetEnv mysql.database "exampleDev"
    </VirtualHost>


Then update the db.php file to fetch the database login info from the Environment Variables.


    return array(
      // The '*' triggers the mult-environment settings
      // Never store database info in repos.
      // All environments get sensitive database info from environment variables.
      '*' => array(
        // The database server name or IP address. Usually this is 'localhost' or '127.0.0.1'.
        'server' => 'localhost',
    
        // The database username to connect with.
        'user' => getenv("mysql.user"),
    
        // The database password to connect with.
        'password' => getenv('mysql.password'),
    
        // The name of the database to select.
        'database' => getenv('mysql.database'),
    
        // The prefix to use when naming tables. This can be no more than 5 characters.
        'tablePrefix' => 'craft',
      )

## Hide index.php files in URIs

### Enable rewrite mod

On MAMP's Apache simply ensure the `mod_rewrite.so` is enabled

  `LoadModule rewrite_module modules/mod_rewrite.so`

## Git Setup  
The /app, /plugins and /storage are not stored in the repo. Upload /app file separately and use one-click updating to maintain craft app files

## File structure
    Local:~/Code         Host:/srv/www
    |-example.com        |-stage.example.com
      |-craft                | |-craft
      | |-app                | | |-app
      | |-config       ==>   | | |-config
      | |-plugins            | | |-plugins
      | |-storage            | | |-storage
      | |-templates    ==>   | | |-templates
      | |-.htaccess    ==>   | | |-.htaccess
      |-public               | |-public
        |-.htaccess    ==>   | | |-.htaccess
        |-index.php    ==>   | | |-index.php
        |-robots.txt   ==>   | |-robots.txt

