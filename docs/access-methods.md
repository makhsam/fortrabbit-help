---

template:      article
reviewed:      2016-07-19
naviTitle:     Access methods
title:         How to access fortrabbit services
lead:          Learn about the different authentication methods with fortrabbit.
group:         Kitchen_sink

otherVersionLinks:
    - code-access-old-app

keywords:
     - ssh key
     - git
     - git-deployment
     - ssh
     - username
     - password
     - access
     - authentication

tags:
     - git

seeAlsoLinks:
    - git
    - git-deployment
    - ssh-keys
    - deployment-architecture-video

---

So far so good: You are using your Account e-mail address and your Account password to login to the fortrabbit [Dashboard](/dashboard). Beside that, there are these interaction channels with fortrabbit services:

* Accessing the [MySQL database](/mysql#toc-remote-mysql-access)
* Accessing the [Object Storage](/object-storage#toc-accessing-the-object-storage)
* Deploying code with [Git](/git-deployment#toc-usage)
* Execution [SSH Remote commands](/remote-ssh-execution#toc-usage)
* Viewing [log files](/logging)

Of course these operations need to be protected. We need to make sure that only you and your team have access. You can choose between two authentication methods: "[password](#toc-password-authentication)" and "[public SSH key](#toc-ssh-key-authentication)".



## How to edit your access method

In the "[Dashboard](/dashboard)", go to "Your Account" (upper right). Under "Access method" you can find your current settings. You see which method is currently set.


### Access schema

```
# Git clone example
$ git clone [[ssh-user]]@deploy.[[region]].frbit.com:[[your-app]].git
```

* With SSH key authentication [[ssh-user]] will be: [[your-app]]
* With password authentication [[ssh-user]] will be: [[your-app]].[[long-user-string]]
* With password authentication you will need to enter your fortrabbit Account password (each time)
* With SSH key authentication your keys will be used
* [[your-app]] is the name of your App (see Dashboard)
* [[region]] is `eu2` or `us1`, depending on the location of your App

URLs and terminal commands depend on your chosen access method. The above example shows the pattern for SSH key authentication with SSH keys.



## SSH key authentication

We recommend to use public SSH key authentication to identify yourself with fortrabbit services. It's more secure than password authentication and also more convenient, once you have set it up. SSH key authentication is commonly used with services like BitBucket and GitHub.



### How to add and remove SSH keys with your Account

In the "Dashboard" > "Your Account" > "Access method" you can "Add a new SSH key". The **[direct link](https://dashboard.fortrabbit.com/account/keys/new)** can also take you there (login and re-authentication maybe required).


### How to create a public SSH key locally

We have a [dedicated article](ssh-keys) on setting up and troubleshooting your local SSH keys.


### How to change from password to SSH key authentication

In the "Dashboard" > "Your Account" > "Access method" you can add an SSH key. Once you have added your first public SSH key, password authentication will be disabled.


### GitHub SSH key import

**Automatic import**: When signing up to fortrabbit, we'll check at GitHub if there are any public SSH keys associated with your e-mail. If we find any, we'll import them and install them with your account. This is a one time setup, your SSH keys will not be synced. You can manage the SSH keys like any other keys then.

**Manual import**: You can also tell the import helper your GitHub account, when using different e-mail addresses here and on GitHub. There is a small link which will take you 

The [direct Dashboard link](https://dashboard.fortrabbit.com/boarding/keys/github) (login and re-authentication maybe required) will take you there directly.


### App-only SSH keys

In certain cases you might want to add code access to an App without the need to register a new Account with fortrabbit. One case is a hectic ad-hoc hotfix scenario (good luck!), another case is that you have some advanced deployment with a third party continuous integration service bot going on. So you can install additional App-only custom public SSH keys with each App. You manage those App-only SSH keys in the Dashboard with your App.


## Password authentication

This is the default method when no public SSH keys are installed. Use this, when you just want to check out fortrabbit or when you have trouble setting up your SSH keys locally, help on this is over [here](ssh-keys).



### How to change from SSH key to password authentication

When for some reason SSH key authentication does not work for you, you can downgrade to password like so: In the "Dashboard" > "Your Account" > "Access method" you can click on your public SSH keys, this will bring up a view where you can delete the key. When deleting the last key, password authentication will be re-enabled.

## Authentication in teams

You manage your access method with your user Account on fortrabbit. This way you always have up-to-date code access on each App you own and collaborate on. It also makes managing the team easy — add/remove collaborators and code access is handled "automagically". Please mind that your team members might have a different acess method and that your settings might not work for them.