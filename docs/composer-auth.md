---

template:    article
reviewed:    2022-07-07
title:       How to generate auth.json for Composer private repos 
naviTitle:   Private Composer repos with auth.json
lead:        Generate an auth.json file during deployment to access private Composer repos.
group:       development
stack:       all
order:       10

keywords:
    - Composer
    - Git
    - ssh
    - GitHub
    - Bitbucket
    - private
---

Modern PHP app development utilizes [Composer](composer) as a dependency manager. There are many great open source packages [out there](http://packagist.org). But your company code is probably not intended to be released to the public. That's when you use private Composer repositories.

In the script below we generate a global auth.json file that contains credentials to access a Github repo using oAuth, and another private repo which is protected with Basic HTTP auth, in our example Laravel Nova.

This is just for the sake of demonstration, you will probably need to adjust it to your needs.

Since you don't want to keep secrets in your git history, you can store them in [Secrets](/secrets) or [Env vars](/env-vars). In the script we show both ways.

```php
<?php

# add-auth.php

$secrets = json_decode(file_get_contents($_SERVER["APP_SECRETS"]), true);
$nova_username = $secrets['CUSTOM']['NOVA_USERNAME'];
$nova_password = $secrets['CUSTOM']['NOVA_PASSWORD'];

$github_oauth =  getenv('GITHUB_OAUTH_TOKEN');

$data = [
    'github-oauth' => [
        ["github.com" =>  $github_oauth]
    ],
    'http-basic' => [
        'nova.laravel.com' => [
            'username' => $nova_username,
            'password' => $nova_password,
        ]
    ]
];
$json = json_encode($data);

$path = '~/.composer/';
$authFile = $path . 'auth.json';
mkdir($path, 0777, true);
file_put_contents($path, $json);
```

The script you created needs to be executed before Composer tries to install packages. Create a fortrabbit.yaml file with the following structure:

```yaml
version: 2
pre: add-auth.php
```

After deploying the two files you are set to access your private repos.

### Link your private repo

Now you can add your private repositories to your `composer.json` file as usual:

```json
    "repositories": [
        {
            "type": "vcs",
            "url":  "git@github.com:my-company/my-package.git"
        }
    ],
    "require": {
        "my-company/my-package": "^1.2.3"
    }
}
```