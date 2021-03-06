yii2-oauth2-server
==================

A wrapper for [Filsh/yii2-oauth2server](https://github.com/Filsh/yii2-oauth2-server) which implement an [OAuth2 Server](https://github.com/bshaffer/oauth2-server-php)

Add missing code that make it easy to use with social network aware user module like the one from [dektrium/yii2-user](https://github.com/dektrium/yii2-user), you can use the [macfly/yii2-authclient-oauth2](https://github.com/Marty-Macfly/yii2-authclient-oauth2) which work with it out off the box.

Add controller:

* [Authorize](http://bshaffer.github.io/oauth2-server-php-docs/controllers/authorize/)
* [Token](http://bshaffer.github.io/oauth2-server-php-docs/controllers/token/)
* User (Provide user information id, username, e-mail, ...)

> /!\ with version 2.0.13 of the Yii framework there is [an issue with Filsh/yii2-oauth2server](https://github.com/Filsh/yii2-oauth2-server/issues/134), you can fix it by updated your `composer.json` with the following

```json
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/Marty-Macfly/yii2-oauth2-server-1"
        }
    ],

...

    "require": {

...

        "filsh/yii2-oauth2-server": "2.0.2.x-dev",

...

    },

...

```


Installation
------------

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist macfly/yii2-oauth2-server "*"
```

or add

```json
"macfly/yii2-oauth2-server": "*"
```

to the require section of your composer.json.

To use this extension,  simply add the following code in your application configuration as a new module:

```php
'modules'=>[
    //other modules .....
    'oauth2' => [
        'class' => 'macfly\oauth2server\Module',
        'tokenParamName' => 'accessToken',
        'tokenAccessLifetime' => 3600 * 24, // Default token lifetime
        'userModel' => 'app\models\User',
        'userAttributes' => [ // List of user attributes you want to provide through the /oauth2/user api call
            'id',
            'username',
            'email',
        ],
    ]
    // yii2-oauth2-server is using the kartik\datecontrol\Module so you should define the configuration of the module
    // See more details at http://demos.krajee.com/datecontrol
    'datecontrol' =>  [
        'class'             => 'kartik\datecontrol\Module',
        'ajaxConversion'    => true,
        'autoWidget'        => true,
        'saveTimezone'      => 'UTC',
    ],
],
```

Can be usefull to enable in requet component the [json parser ](http://www.yiiframework.com/doc-2.0/guide-rest-quick-start.html#enabling-json-input) and [pretty url](http://www.yiiframework.com/doc-2.0/guide-runtime-routing.html#using-pretty-urls).

Also, extend ```app\models\User``` - user model - implementing the interface ```\OAuth2\Storage\UserCredentialsInterface```, so the oauth2 credentials data stored in user table.

You should implement (for convenience a trait is provide):
- findIdentityByAccessToken()
- checkUserCredentials()
- getUserDetails()
- getAuthKey()
- getOauthClient() mapping of one user to one oauth client.

You can extend the model if you prefer it (please, remember to update the config files) :

```
use Yii;

class User extends app\models\User implements \OAuth2\Storage\UserCredentialsInterface
{
	use \macfly\oauth2server\traits\Oauth2User;
}
```

The next step you should run migration

```php
yii migrate --migrationPath=@vendor/macfly/yii2-oauth2-server/src/migrations
```

this migration create the oauth2 database scheme and insert test user credentials ```testclient:testpass``` for ```http://127.0.0.1:8888/user/security/auth?authclient=oauth2```

Usage
------------

# List of available actions

- **/oauth2/authorize**: Session verification from a browser
- **/oauth2/token**: Get token
- **/oauth2/user**: Get user information

You can see the filsh documentation to use token (https://github.com/Filsh/yii2-oauth2-server/tree/v2.0.0#usage)

Admin interface
-----

Manage client credentials

- **/oauth2/clients**

CRUD operations

- **/oauth2/clients/index**
- **/oauth2/clients/create**
- **/oauth2/clients/update**
- **/oauth2/clients/delete**

Manage access token

- **/oauth2/accesstokens**

CRUD operations

- **/oauth2/accesstokens/index**
- **/oauth2/accesstokens/create**
- **/oauth2/accesstokens/update**
- **/oauth2/accesstokens/delete**
