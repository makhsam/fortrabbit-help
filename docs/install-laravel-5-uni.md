---

template:         article
reviewed:         2022-03-21
title:            Install Laravel 5
naviTitle:        Laravel
lead:             Laravel is the most PHPopular framework. Learn how to install and tune Laravel 5 on fortrabbit.
group:            Install_guides

websiteLink:      http://laravel.com
websiteLinkText:  laravel.com
category:         framework
image:            laravel-black-new.svg
version:          5.8
stack:            uni
proLink:          install-laravel-5-pro

deprecated:       yes
dontList:         true
dontIndex:        true

otherVersions:
    8: install-laravel-8
    9: install-laravel-9
    10: install-laravel
    
keywords:
    - php
    - install
    - laravel

---

## Get ready

Please make sure to have followed our [get ready guide](/get-ready) before starting here.

MIND: This the install guide for Laravel version 5. We have an [Laravel install guide](/install-laravel-9) for a newer version (9). Check out that article as well, it includes more up-to-date information.


## Quick start

Following the fastest way to start with a fresh installation. Please scroll below for [migrating an existing Laravel](#toc-database-migration). Execute the following in your terminal **on your local machine**:

```bash
# 1. Use Composer to create a local Laravel project named like your App
$ composer create-project laravel/laravel --prefer-dist {{app-name}}
# this installs Laravel with Composer locally and will take a while

# 2. Change into the folder
$ cd {{app-name}}

# 3. Initialize a local Git repo
$ git init .

# 4. Add all files
$ git add -A

# 5. Commit files for the first time
$ git commit -m 'Initial'

# 6. Add fortrabbit as a remote
$ git remote add fortrabbit {{ssh-user}}@deploy.{{region}}.frbit.com:{{app-name}}.git

# 7. Push changes to fortrabbit
$ git push -u fortrabbit main
# this installs Laravel with Composer on remote and take while

# the next deployments will be much faster
# 8. Push from now on
$ git push
```

**Got an error?** Please see the [access troubleshooting](/access-methods#toc-troubleshooting). **Did it work?** Cool, when this is done, you can visit your App URL in the browser to see the Laravel welcome screen:

* [{{app-name}}.frb.io](https://{{app-name}}.frb.io)


## Setup and migration

**Don't stop with a plain vanilla installation. Make it yours!** Check out the following topics if you have an existing Laravel installation or if you would like to setup Laravel so that you can run in a local development environment as well as in your fortrabbit App:

### Setup Git for an existing application

If you used the above Quick start guide, you have a plain vanilla installation, Git is already setup and you can safely skip this topic. If you haven't and you have a local Laravel already running, follow steps 3 to 6 from above, to initialize a local Git repo and add your fortrabbit remote or have a look at our general [getting started with Git guide](/git). When moving from another host to fortrabbit, please also read our [migration guide](/migrating) as well.

### MySQL configuration

If you have chosen Laravel in the [Software Preset](app#toc-software-preset) when creating your App, we will automatically populate the "right" environment variables for the MySQL connection. So, you don't need to change a thing, just keep `config/database.php` as is:

```php
<?php
return [
    // keep above
    'connections'   => [
        // keep above
        'mysql' => [
             'driver' => 'mysql',
             'host' => env('DB_HOST', '127.0.0.1'),
             'port' => env('DB_PORT', '3306'),
             'database' => env('DB_DATABASE', 'forge'),
             'username' => env('DB_USERNAME', 'forge'),
             'password' => env('DB_PASSWORD', ''),
             'unix_socket' => env('DB_SOCKET', ''),
             'charset' => 'utf8mb4',
             'collation' => 'utf8mb4_unicode_ci',
             'prefix' => '',
             'prefix_indexes' => true,
             'strict' => true,
             'engine' => null,
         ],
        // keep below
    ],
    // keep below
];
```

If you have not chosen the Laravel in the [Software Preset](app#toc-software-preset) on App creation, then please make sure to [set the ENV vars as described above](#toc-env-vars).


### Database import and export

There are various use cases to export and import the database:

1. Export the database from your old web hosting
2. Export your local database to import it to the fortrabbit database
3. Export the remote database from fortrabbit to bring your local installation up-to-date

#### Import/export the database with a GUI

Read on in the [MySQL Article: Export & import > Using MySQL Workbench (GUI)](mysql-uni#toc-using-mysql-workbench-gui-).

#### Import/export the database in the terminal

Read on in the [MySQL Article: Export & import > Using the terminal](mysql-uni#toc-using-the-terminal).

#### MySQL access from local

Please see the [MySQL article](mysql-uni#toc-access-mysql-from-local) on how to access the database remotely from your computer.

### Update database with artisan migrate command

You can either login to [SSH](ssh-uni) and execute `artisan` or utilize [execute remote commands via SSH](/remote-ssh-execution-pro), for example:

```bash
# remote execution
$ ssh {{ssh-user}}@deploy.{{region}}.frbit.com 'php artisan migrate --force'

# login and execute
$ ssh {{ssh-user}}@deploy.{{region}}.frbit.com
$ php artisan migrate --force
```

**Note**: If `APP_ENV` is set to `production` - which is the default - then Laravel expects `--force` for migrate commands.

You can also add this command to your `composer.json` to have it run automatically every time you push changes.

```json
"scripts": {  
    "post-install-cmd": [
        "php artisan migrate --no-interaction --force",
    ],
}
```

With that in place, any time you deploy your code, database changes will be applied immediately. If no database changes are required, nothing happens, so it is safe to run all the time. Just make sure to test your upgrades and migrations locally first.


### Logging

You can access your logs via SSH or SFTP. Laravel, per default, stores it's log files in `storage/logs`. You can download them via SFTP from that folder. If you need to "tail" your logs live, you can:

```bash
# login via SSH
$ ssh {{ssh-user}}@deploy.{{region}}.frbit.com

# tail the logs (they contain the current date, per default)
$ tail -f storage/logs/laravel-$(date '+%Y-%m-%d').log
```

### Queues

The Universal stack does not support long running processes like `php artisan queue:work`.
Please check out the [Laravel 5 Professional article](install-laravel-5-pro#toc-queue) on how to integrate those.

#### Using envoy

Easy. Here is an `Envoy.blade.php` example:

```php
@servers(['fr' => '{{ssh-user}}@deploy.{{region}}.frbit.com'])

@task('ls', ['on' => 'fr'])
    ls -lha
@endtask

@task('migrate', ['on' => 'fr'])
    php artisan migrate
@endtask
```

Then execute locally:

```bash
$ envoy run ls
$ envoy run migrate
```

### Laravel Mix

Laravel Mix compiles JS and CSS to really small and handy files using webpack, also see the [Laravel docs on this](https://laravel.com/docs/mix). You can not on fortrabbit as there is [no Node] on remote running. So you need to run the built process for production locally first.

```
# Install node modules (locally)
npm install

# Build assets using Laravel Mix (locally)
npm run prod
```

For deploying the assets we recommend using `rsync`. The one-liner below works with the most common scenarios, adjust it to your needs if your setup differs.

```
# Rsync command to sync the asset in your /public folder 
rsync -av --include='*.js' --include='*.css' --include='mix-manifest.json' --exclude='*'  ./public/ {{app-name}}@deploy.{{region}}.frbit.com:./public/
```

For your convenience you can define rsync command `npm run deploy-assets` ...

```package.json
{
  "scripts": {
    "deploy-assets": "rsync -av ...",
   }
}
```

### Scheduling

The [Laravel scheduler](https://laravel.com/docs/5.8/scheduling) is not supported with the Universal Stack by design. The minimum time frame for standard crons is 10 minutes here, but the Laravel scheduler requires a 1 minute scheduling. Use the [Pro Stack](/app-pro) in combination with the [Workers Component](/worker-pro). That way your crons will be outsourced into background processes. 


### Sending mail

You can not use [sendmail](quirks#toc-mailing) on fortrabbit but Laravel provides a API over the popular SwiftMailer library. The mail configuration file is `app/config/mail.php`, and contains options allowing you to change your SMTP host, port, and credentials, as well as set a global form address for all messages delivered by the library.
