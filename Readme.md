#  LAMP stack built with Docker Compose

I customized this docker-compose setup to run camptree.
- Removed Redis  
- Updated sample.env
- Hardcoded a session save path in docker-compose TODO - move to sample.env
- Made a copy of config/php.ini to pma-php.ini so there would not be a conflict
- Modified config/php/php.ini to use the session save path

A few notes: 
- mysql puts its data in data/mysql. Data will persist despite containers
  being shutdown or images wiped out. If you change mysql versions you 
  will probably need to wipe this directory out.
- phpmyadmin puts session data in the pma directory
- I fought with the php session storage for a while. As a result there
  are two different php.ini files, one for the main server, and one
  for PPHMyAdmin. There is probably a better way.
  
A `database.php` setup for this docker network file needs to be put in camptree/application/config
```
$db['default'] = array(
    'dsn' => '',
    //'hostname' => 'camps.camptree.org',
    'hostname' => 'camptree-database',
    'username' => 'camptree',
    'password' => 'camptree@123',
    'database' => 'camptree1',
    'dbdriver' => 'mysqli',
    'dbprefix' => '',
    'pconnect' => FALSE,
    'db_debug' => (ENVIRONMENT !== 'production'),
    'cache_on' => FALSE,
    'cachedir' => '',
    'char_set' => 'utf8',
    'dbcollat' => 'utf8_general_ci',
    'swap_pre' => '',
    'encrypt' => FALSE,
    'compress' => FALSE,
    'stricton' => FALSE,
    'failover' => array(),
    'save_queries' => TRUE
);
```

Here is a sample directory structure that should work with this docker-compose
setup:

- camptree-projects
  - camptree (contains the camptree codeigniter version)
  - camptree-docker (this repo)

From the camptree-docker directory you should be able to run 
`docker-compose up  -d` after setting up you `.env` file. If 
you're having startup problems, run that command without the 
`-d` to make it easier to get to the outputof the various 
containers.

When you need to rebuild the project run 
`docker-compose up --build -d.`

![Landing Page](https://preview.ibb.co/gOTa0y/LAMP_STACK.png)

A basic LAMP stack environment built using Docker Compose. It consists of the following:

* PHP
* Apache
* MySQL
* phpMyAdmin
* Redis

As of now, we have several different PHP versions. Use appropriate php version as needed:

* 5.4.x
* 5.6.x
* 7.1.x
* 7.2.x
* 7.3.x
* 7.4.x

> Please note that we simplified the project structure from several branches for each php version, to one centralized master branch. Please let us know if you encouter any problems. 
##  Installation
 
* Clone this repository on your local computer
* configure .env as needed 
* Run the `docker-compose up -d`.

```shell
git clone https://github.com/sprintcube/docker-compose-lamp.git
cd docker-compose-lamp/
cp sample.env .env
// modify sample.env as needed
docker-compose up -d
// visit localhost
```

Your LAMP stack is now ready!! You can access it via `http://localhost`.

##  Configuration and Usage

### General Information 
This Docker Stack is build for local development and not for production usage.

### Configuration
This package comes with default configuration options. You can modify them by creating `.env` file in your root directory.
To make it easy, just copy the content from `sample.env` file and update the environment variable values as per your need.

### Configuration Variables
There are following configuration variables available and you can customize them by overwritting in your own `.env` file.

---
#### PHP
---
_**PHPVERSION**_
Is used to specify which PHP Version you want to use. Defaults always to latest PHP Version. 

_**PHP_INI**_
Define your custom `php.ini` modification to meet your requirments. 

---
#### Apache 
---

_**DOCUMENT_ROOT**_

It is a document root for Apache server. The default value for this is `./www`. All your sites will go here and will be synced automatically.

_**VHOSTS_DIR**_

This is for virtual hosts. The default value for this is `./config/vhosts`. You can place your virtual hosts conf files here.

> Make sure you add an entry to your system's `hosts` file for each virtual host.

_**APACHE_LOG_DIR**_

This will be used to store Apache logs. The default value for this is `./logs/apache2`.

---
#### Database
---

_**DATABASE**_
Define which MySQL or MariaDB Version you would like to use. 

_**MYSQL_DATA_DIR**_

This is MySQL data directory. The default value for this is `./data/mysql`. All your MySQL data files will be stored here.

_**MYSQL_LOG_DIR**_

This will be used to store Apache logs. The default value for this is `./logs/mysql`.

## Web Server

Apache is configured to run on port 80. So, you can access it via `http://localhost`.

#### Apache Modules

By default following modules are enabled.

* rewrite
* headers

> If you want to enable more modules, just update `./bin/webserver/Dockerfile`. You can also generate a PR and we will merge if seems good for general purpose.
> You have to rebuild the docker image by running `docker-compose build` and restart the docker containers.

#### Connect via SSH

You can connect to web server using `docker-compose exec` command to perform various operation on it. Use below command to login to container via ssh.

```shell
docker-compose exec webserver bash
```

## PHP

The installed version of depends on your `.env`file. 

#### Extensions

By default following extensions are installed. 
May differ for PHP Verions <7.x.x

* mysqli
* pdo_sqlite
* pdo_mysql
* mbstring
* zip
* intl
* mcrypt
* curl
* json
* iconv
* xml
* xmlrpc
* gd

> If you want to install more extension, just update `./bin/webserver/Dockerfile`. You can also generate a PR and we will merge if it seems good for general purpose.
> You have to rebuild the docker image by running `docker-compose build` and restart the docker containers.

## phpMyAdmin

phpMyAdmin is configured to run on port 8080. Use following default credentials.

http://localhost:8080/  
username: root  
password: tiger

## Redis

It comes with Redis. It runs on default port `6379`.

## Contributing
We are happy if you want to create a pull request or help people with their issues. If you want to create a PR, please remember that this stack is not built for production usage, and changes should good for general purpose and not overspecialized. 
> Please note that we simplified the project structure from several branches for each php version, to one centralized master branch.  Please create your PR against master branch. 
> 
Thank you! 

## Why you shouldn't use this stack unmodified in production
We want to empower developers to quickly create creative Applications. Therefore we are providing an easy to set up a local development environment for several different Frameworks and PHP Versions. 
In Production you should modify at a minimum the following subjects:

* php handler: mod_php=> php-fpm
* secure mysql users with proper source IP limitations
