---
layout: post
title: Craft CMS Install Setup

---

Step-By-Step Craft CMS Install and Config For Multi-Environment. The following guide combines information from Craft CMS documentation, several blog posts and StackOverflow answers.

## Local Environment Setup

### Overview

1. Setup our local machine with a basic Craft Config
	- Install MAMP
	- Configure Apache Virtual Hosts
	- Create Database and configure
	- Install Craft 
	- Basic Craft Config and Initialize
2. Setup the Host environment 
	- Install Apache, mySql and PHP, plus required PHP extensions
	- Configure Apache Virtual Hosts
	- Create Database and configure
	- Upload Craft 
	- Basic Craft Config and Initialize
3. Setup Multi Environment Config 

### File structure and repo setup

Craft uses a one-click updating process to keep itself up-to-date, because of this, we won't commit the Craft app code to Git. Likewise, we won't commit `/plugins`. And the `/storage` directory is a working directory for caching and such so that won't be committed.

The file structure will look something like this

The /app, /plugins and /storage directories will not stored in the repo. 

    Local Application		GitHub Repo
    Local:~/Code              Github
      |-example.com        	  |-example.com
        |-craft                | |-craft
        | |-app                 | | |
        | |-config       ==>    | | |-config
        | |-plugins             | | |-
        | |-storage             | | |-
        | |-templates    ==>    | | |-templates
        | |-.htaccess    ==>    | | |-.htaccess
        |-public                | |-public
          |-.htaccess    ==>    | | |-.htaccess
          |-index.php    ==>    | | |-index.php
          |-robots.txt   ==>    | |-robots.txt

### Install MAMP

Simply download and install MAMP. Open the start page to verify it is working.

### Setup Git Repo for Project

In github, create a repo named "example.com"
Clone git repo to local environment
	
    cd ~/
    mkdir Code
    cd ~/Code/
    git clone https://github.com/cklanac/example.com.git

### Download and install Craft

Download Craft
Move the `/craft` and `/public` directories to `~/Code/example.com/` directory
Rename htaccess to `.htaccess`.

	cd ~/Code/example/pubic
	mv htaccess .htaccess

Your directory structure should look something like this:

    Local Application
    Local:~/Code     
    |-example.com  
    |-craft      
    | |-app      
    | |-config   
    | |-plugins  
    | |-storage  
    | |-templates
    | |-.htaccess
    |-public     
      |-.htaccess
      |-index.php
      |-robots.txt

Set permissions on the following:

	cd ~/Code/example.com/craft/
	chmod -R 744 app/
	chmod -R 744 config/
	chmod -R 744 storage/

Add files and push to github

	git add .
	git commit -m 'add craftcms files'
	git push orgin master

### Improve .gitignore

Go to https://www.gitignore.io and lookup CraftCMS
Append the example to .gitignore

	# Created by https://www.gitignore.io/api/craftcms

	### CraftCMS ###
	# Craft Storage (cache) [http://buildwithcraft.com/help/craft-storage-gitignore]
	/craft/storage/*
	!/craft/storage/logo/*

As mentioned above, Craft provides one-click updating so we don't need to keep the Craft CMS `/app` files in the repo. Add the following folders to the .gitignore

	/craft/app/
	/craft/plugins/

### Local Virtual Host Config

MAMP Document Root point the `/htdocs` in the install folder. For a simple setup you could point the Document Root to `~/Code/example.com/public`. But we want to use Virtual Hosts in Apache

Open: 

	/Applications/MAMP/conf/apache/extra/httpd-vhosts.conf

Configure a very simple Virtual Hosts in Apache (MAMP). 

    <VirtualHost *:80>
        DocumentRoot "/Users/Chris/Code/example.com/public"
        ServerName local.example.dev
    </VirtualHost>

And now, create an entry in the `hosts`. In `/private/etc/hosts`, add the following line:

    127.0.0.1 local.example.dev
        
Let's test out the Virtual Host configuration. At this point, we haven't created the DB yet so Craft won't work. But you can drop in a phpInfo file and test the current config.

Create a phpInfo file
	
	cd /Users/Chris/Code/example.com/public
	touch info.php
	nano info.php

And add the following to the file:

	<?php
		phpinfo();
	?>

Now point your browser to `http://local.example.dev` Apache will match the domain with the VirtualHost ServerName entry. If it matches then it will serve up the content `/Users/Chris/Code/example.com/public`.

### Create Database

Go to phpMyAdmin > Databases > Create Database `example`
Note the username and pass-phrase

### Update Craft DB Config 

First we'll setup a basic config, later we'll setup multi-environment configs.
Update `craft/config/db.php` Config 

    return array(
        // The database server name or IP address. Usually this is 'localhost' or '127.0.0.1'.
        'server' => 'localhost',
    
        // The database username to connect with.
        'user' => 'root',
    
        // The database password to connect with.
        'password' => 'passphrase',
    
        // The name of the database to select.
        'database' => 'example',
    
        // The prefix to use when naming tables. This can be no more than 5 characters.
        'tablePrefix' => 'craft',
    );

### Initialize Craft

Open http://local.example.dev/index.php/admin and click Begin
Follow the install steps.

### Multi-Environment Config

Now that we know the basic configuration works, let's add multi-environment configs so our deployment will be easier. Multi environment configs provide support for different configurations on various environments which eliminates the need to setting and resetting configs as the code moves thru the deployment pipeline.

If you decide to skip multi-environment configs, then you should also exclude the following config the DB config since it contains sensitive info.

	/craft/config/db.php

Our approach for the multi-environment config is to set production values under '*' and only override values for dev.example.com, stage.example.com. Essentially production is default and other environments override as necessary.

    return array(
        '*' => array(
            // The database server name or IP address. Usually this is 'localhost' or '127.0.0.1'.
            'server' => 'localhost',
    
            // The database username to connect with.
            'user' => 'root',
    
            // The database password to connect with.
            'password' => 'TBD',
    
            // The name of the database to select.
            'database' => 'TBD',
    
            // The prefix to use when naming tables. This can be no more than 5 characters.
            'tablePrefix' => 'craft',
        ),
        'localhost' => array(
            // The database username to connect with.
            'user' => 'root',
    
            // The database password to connect with.
            'password' => 'password-phrase',
    
            // The name of the database to select.
            'database' => 'example',
        ),
    );

Test this out be refreshing your browser, basically the site should work as before

### Improve Security

We can add a layer of security by moving the DB password into the Virtual Hosts config. It is not perfect since the password is available as a PHP variable, but that is a necessary evil. Never-the-less, it is very unlikely that the `httpd-vhosts.conf` would be committed and pushed to a Public repo.

Append the SetEnv lines to the /Applications/MAMP/conf/apache/extra/httpd-vhosts.conf

    <VirtualHost *:80>
        DocumentRoot "/Users/Chris/Code/example.com/public"
        ServerName local.example.dev
            
        # Use environment variables to store passwords more securely
        # Multi Environment configuration (See craft/config/db.php)
        SetEnv mysql.password "password-phrase"
    </VirtualHost>

### Update DB Config 

Now we'll remove the passphrase from the `db.php` file and replace it with a `getenv()` call. Update the DB config:
 
Update /craft/config/db.php. 

Replace:

	'password' => 'passwordphrase',

With:

	'password' => getenv('mysql.password'),

The Localhost section will look something like this:

    ...
    'localhost' => array(
        // The database username to connect with.
        'user' => 'root',
    
        // The database password to connect with.
        'password' => getenv('mysql.password'),
    
        // The name of the database to select.
        'database' => 'example',
    ),
    ...

Server Environment Setup
----------

> Depending on you host environment, your configuration may be slightly different.

### Create Stage and Production directories
For small sites you can have the staging and production next to each other like the following diagram. This is convenient and works for small sites. But should not be used for large mission critical setups


	Host:/srv/www
    |-example.com/
    |-stage.example.com/
    | |-craft/
    | |-public/
    |-www.example.com/
    | |-craft/
    | |-public/

### Upload the Craft App to Server

* Upload "craft" directory to cd "/srv/www/stage.example.com/"
* Upload the contents of "public to "/srv/www/stage.example.com/public"

### Create Databases with Users and Passwords

    mysql -u root -p
    Enter password:
    mysql> create database example;
    Query OK, 1 row affected (0.00 sec)
    
    mysql> grant all on example.* to 'webuser' identified by '<password>';
    
    mysql> flush privileges;
    Query OK, 0 rows affected (0.00 sec)
    
    mysql> quit
    Bye

### Install and Enable required apache modules

Depending on your setup you may need to apt-get the modules, or you might simple need to enable them. See [Craft Server and PHP Extension Requirements](https://craftcms.com/docs/requirements)

On Debian/Ubuntu run `a2enmod` to enable modules, the restart Apache:

	apt-get install mcrypt php5-mcrypt
	apt-get install php5-imagick
	apt-get install curl
	
	a2enmod rewrite
	a2enmod expires
	php5enmod mcrypt
	service apache2 restart

### Virtual Host Config:

Config Virtual Hosts in Apache (MAMP).

In /etc/apache2/sites-available/example.com

    <VirtualHost *:80>
        ServerAdmin cklanac@email.com
        ServerName  www.example.dev
        ServerAlias example.com
    
        # Index file and Document Root (where the public files are located)
        DirectoryIndex index.html index.php
        DocumentRoot /srv/www/example.com/www.example.com/public
    
        # Log file locations
        LogLevel warn
        ErrorLog  /srv/www/example.com/www.example.com/log/error.log
        CustomLog /srv/www/example.com/www.example.com/log/access.log combined
        
                # Use environment variables to store passwords more securely
        # Multi Environment configuration (See craft/config/db.php)
        SetEnv mysql.password "password-phrase"
    </VirtualHost>
    
    <VirtualHost *:80>
        ServerAdmin cklanac@email.com
        ServerName  stage.example.dev
    
        # Index file and Document Root (where the public files are located)
        DirectoryIndex index.html index.php
        DocumentRoot /srv/www/example.com/stage.example.com/public
    
        # Log file locations
        LogLevel warn
        ErrorLog  /srv/www/example.com/stage.example.com/log/error.log
        CustomLog /srv/www/example.com/stage.example.com/log/access.log combined
    
                # Use environment variables to store passwords more securely
        # Multi Environment configuration (See craft/config/db.php)
        SetEnv mysql.password "password-phrase"
    </VirtualHost>
    
Refresh your browser http://stage.example.com/
At this point you should see:

	/srv/www/stage.example.com/craft/config isn't writable by PHP. Please fix that.

### Fix the permissions
	
	chown -R www-data:www-data craft/
	cd craft/
	chmod -R 744 app/
	chmod -R 744 config/
	chmod -R 744 storage/
	
	cd ../public	
	chmod -R 744 assets

### Increase PHP Memory
Increase PHP memory to 64M. 128 M max

/etc/php5/apache2/php.ini

    ; Maximum amount of memory a script may consume (128MB)
    ; http://php.net/memory-limit
    memory_limit = 128M

### Transfer Dev (localhost) Database to Server
Here's a help guide as a reference:
[How To Migrate a MySQL Database Between Two Servers](https://www.digitalocean.com/community/tutorials/how-to-migrate-a-mysql-database-between-two-servers)

In localhost console
Create backup or dump of DB.

    /Applications/MAMP/Library/bin/mysqldump -u root -p --opt exampleDB > ~/exampleDB-backup-12312015.sql
 
    mysqldump -u root -p --opt manciniTestDB > ~/manciniTestDB-12282015.sql

Copy to Server

    scp ~/exampleDB-backup-12312015.sql root@12.34.56.78:~/
 

sidenote:

    scp root@45.55.135.32:willmanciniProd-20160801.sql ~/


In Server console (ssh session)
Load Import database

    ssh root@12.34.56.78
    mysql -u root -p exampleDB < ~/backup.sql
    
    mysql -u root -p willmanciniStage < ~/manciniTestDB-12282015.sql
    mysql -u root -p goddreamsDB < ~/willmancinibackup-12312015.sql 


### Keep repo up to date

Local environment

    git commit -m 'awesome update'
    git push origin master

Linode environment

    git pull origin master

### Hide index.php files in URIs

[Removing “index.php” from URLs](https://craftcms.com/help/remove-index.php)

In `<path-to-website>/public/.htaccess

    <IfModule mod_rewrite.c>
        RewriteEngine On
    
        # Send would-be 404 requests to Craft
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteCond %{REQUEST_URI} !^/(favicon\.ico|apple-touch-icon.*\.png)$ [NC]
        RewriteRule (.+) index.php?p=$1 [QSA,L]
    </IfModule>


[1]: http://buildwithcraft.com/docs/installing
[2]: http://a73cram5ay.blogspot.com/2015/04/installing-craft-cms-on-mac-os-x-using.html
[3]: http://buildwithcraft.com/docs/multi-environment-configs
[4]: http://craftcms.stackexchange.com/questions/55/git-workflow-and-multi-environment-how-to-approach
[5]: http://craftcms.stackexchange.com/questions/10/whats-the-best-practice-for-handling-data-migration-and-organization-across-dev
[6]: http://craftcms.stackexchange.com/questions/13/whats-the-best-practice-for-upgrading-a-versioned-craft-site
[7]: http://www.quillstudios.com.au/bloglist/2015/deploying-craft-cms-using-git-in-a-shared-hosting-environment
[8]: http://craftcms.stackexchange.com/questions/2576/getting-craftpath-error-on-server-trying-to-get-craft-running
[9]: http://craftcms.stackexchange.com/questions/6615/craft-install-locally-with-mamp-pro
[10]: http://pixelandtonic.com/blog/test-craft-client-pro-from-any-non-public-domain
[11]: http://buildwithcraft.com/help/license-enforcement#how-do-we-determine-craft-is-running-on-a-public-domain
