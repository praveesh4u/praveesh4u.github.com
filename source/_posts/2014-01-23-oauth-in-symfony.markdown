---
layout: post
title: "OAuth in Symfony 2"
date: 2014-01-23 00:33:00 +0530
comments: true
categories: 
---

#Setting up HWIOAuth Bundle#

**1. Add the HWI OAuth bundle to the project**
 
 Extract  OAuthBundle.zip file atached alongwith this and copy the contents to ProjectRoot/vendor/bundles/HWI/Bundle/OAuthBundle

**2. Enable the bundle in AppKernel.php**
``` php AppKernel.php
public function registerBundles()
    {
        $bundles = array(
            .............................
            new HWI\Bundle\OAuthBundle\HWIOAuthBundle(),
        );

        .................................

        return $bundles;
    } 
```
 HWI OAuth bundle needs Sensio Buzz bundle for proper functioning. So, add the buzz bundle to your project if it is not already added. If it is not added,
 you will get an error message like this

``` html Error
 Fatal error: Class 'Buzz\Client\Curl' not found in /home/xxxx/my-projects/OAuthProject/app/cache/dev/appDevDebugProjectContainer.php on line 2110 Call Stack: 0.0001 321828 1
``` 
 
 To avoid this, copy the contents of buzz.zip atached along with this to PROJECT_ROOT/vendor

 Also, add the namespace for the OAuth bundle and the Buzz bundle to autoload.php
 
``` php Autoload.php
 $loader->registerNamespaces(array(
   'Buzz'=>__DIR__.'/../vendor/buzz/lib',
    'HWI'=>__DIR__.'/../vendor/bundles',
 ));
```

Register the buzz bundle namespace before HWI bundle's namespace to avoid this error
``` html Error
 "Fatal error: Class 'Buzz\Client\Curl' not found" 
```  

**3. Import the routing file of HWI bundle to the projects routing.yml**
``` yaml Routing.yml 
 hwi_oauth_redirect:
    resource: "@HWIOAuthBundle/Resources/config/routing/redirect.xml"
    prefix:   /connect
```
**4. Import the security_factory.xml to security.yml**
``` yaml security.yml
security:
    factories:
        - "%kernel.root_dir%/../vendor/bundles/HWI/Bundle/OAuthBundle/Resources/config/security_factory.xml" 
```
**5.Add the resource owners in the config.yml. **
``` yaml config.yml 
 hwi_oauth:
     #name of the firewall in which this bundle is active, this setting MUST be set
    
    firewall_name: main(What ever firewall name that you have specified in security.yml)
    target_path_parameter: /
    resource_owners:
        facebook:
            type:                facebook
            client_id:           48000000093708
            client_secret:       cda9csdsdsds8dc4dc0699448385c9016c7
            scope:               "email"
            
        google:
            type:                google
            client_id:           872410845454.apps.googleusercontent.com
            client_secret:       _rFBqWyAGHFvbdN-EgUs52uGK2Z
            scope:               "https://www.googleapis.com/auth/userinfo.email https://www.googleapis.com/auth/userinfo.profile"
```            
**6.Create a User Provider Service**

The bundle needs a service that is able to load users based on the user response of the oauth endpoint. If you have a custom service it should implement the interface: HWI\Bundle\OAuthBundle\Security\Core\User\OAuthAwareUserProviderInterface.
For this, create a service named  xyz.oauth.user_provider.
For this, first create a class caller XYZOAuthUserProvider and register it as a service.

``` php OAuthUserProvider.php
<?php

namespace XYZ\CoreBundle\Services;

use HWI\Bundle\OAuthBundle\Security\Core\User\OAuthUserProvider;


class XYZOAuthUserProvider extends OAuthUserProvider
{
    
    
}
```
Then, register it as a service in services.yml file
``` yaml services.yml
xyz.oauth.user_provider:
    class: XYZ\CoreBundle\Services\XYZOAuthUserProvider
    tags:
      - { name: user.provider }
    arguments: ["@service_container"]
```
**7. Modifications in the security.yml file**
######1. Under Providers, add the service created in the previous step

``` yaml security.yml
	providers:
        main:
            entity: { class: XYZCoreBundle:User }
        my_custom_hwi_provider: { id: tsz.oauth.user_provider }
```
#####2. Under Firewalls/main, add

``` yaml security.yml
oauth:
        resource_owners:
            facebook:           "/login/check-facebook"
            google:             "/login/check-google"
        login_path:        /connect/facebook
        failure_path:      /login
        default_target_path: /login
        oauth_user_provider:
            service: xyz.oauth.user_provider
```
**8. Import the login check routes corresponding to each resource owner in the routing.yml file**
``` yaml routing.yml 
 	facebook_login:
    	pattern: /login/check-facebook

	google_login:
    	pattern: /login/check-google 
```

    	       

#CHECKING WHETHER THE USER EXISTS IN THE DATABASE AND GIVING PRIVILEGES ACCORDINGLY#

By default, a user who logs in through facebook or google is given ROLE_USER, and ROLE_OAUTH_USER. In this case, even though all the details required for a registered user are not available from Google or Facebook, the user still have all the privileges of ROLE_USER. This should not be the case. The user should
not be granted all the provileges unless he completes his profile. So, for a user who logs in through Google or Facebook is given only ROLE_OAUTH_USER if he is not present in the database. If he is present in the database, he is granted ROLE_USER when he logs in through Google or Facebook. So, there should be a mechanism to check whether the OAUth user is present in the database or not.

**Step 1**<br>
Edit vendor/bundles/HWI/Bundle/OAuthBundle/Security/Core/User/OAuthUser.php 
and modify the getRoles() function to return only ROLE_OAUTH_USER as role.

Original function:
``` php Original function
public function getRoles()
    {
        return array('ROLE_USER', 'ROLE_OAUTH_USER');
    }
```
Modified function:
``` php Modified function
public function getRoles()
    {
        return array('ROLE_OAUTH_USER');
    } 
```
**Step 2** 

Next, edit the OAuthUserProvider.php file 

>>+ Import the namespace of the bundle containing the User entity to OAuthUserProvider.php so that we can get the container inside OAuthUserProvider.php.
``` php OAuthUserProvider.php
    use Venom\XYZBundle\XYZBundle ;
```
>>+ In the original configuration loadUserByOAuthUserResponse() function calls the loadUserByUsername() function with nickname as  the arguement. The nickname is obtained from the server response. In our entity user provider, we have defined email as a unique field. So, to check whether user exists in the database, we can use email. For this, we need to obtain the email from the response.

Although UserResponseInterface class has a getEmail() method, this won't return the email address for all the service providers as the response for each service is different

A var_dump() for the response object for Facebook and Google are given below

Facebook:
``` html Facebook Response Dump
    ["paths":protected]=>
  array(5) {
    ["identifier"]=>
    string(2) "id"
    ["nickname"]=>
    string(8) "username"
    ["realname"]=>
    string(4) "name"
    ["email"]=>
    NULL
    ["profilepicture"]=>
    NULL
  }
  ["response":protected]=>
  array(13) {
    ["id"]=>
    string(15) "1XXXX5898097313"
    ["name"]=>
    string(12) "XXXXXX"
    ["first_name"]=>
    string(9) "XXXXX"
    ["last_name"]=>
    string(2) "YY"
    ["link"]=>
    string(40) "https://www.facebook.com/xxxx.yyy"
    ["username"]=>
    string(15) "xxxx.yyy"
    ["birthday"]=>
    string(10) "04/14/1900"
    ["gender"]=>
    string(4) "male"
    ["email"]=>
    string(20) "xxxx@yyyy.com"
    ["timezone"]=>
    float(5.5)
    ["locale"]=>
    string(5) "en_US"
    ["verified"]=>
    bool(true)
    ["updated_time"]=>
    string(24) "2013-07-19T09:59:22+0000"
  }
```
Google:
``` html Google response dump
  ["paths":protected]=>
  array(5) {
    ["identifier"]=>
    string(2) "id"
    ["nickname"]=>
    string(4) "name"
    ["realname"]=>
    string(4) "name"
    ["email"]=>
    string(5) "email"
    ["profilepicture"]=>
    string(7) "picture"
  }
  ["response":protected]=>
  array(8) {
    ["id"]=>
    string(21) "10493450YYYY981597842"
    ["email"]=>
    string(28) "praveesh@abc.in"
    ["verified_email"]=>
    bool(true)
    ["name"]=>
    string(10) "Praveesh A"
    ["given_name"]=>
    string(8) "Praveesh"
    ["family_name"]=>
    string(1) "A"
    ["locale"]=>
    string(2) "en"
    ["hd"]=>
    string(19) "abc.in"
  }
```
From the variable dump, we can see that the email address can be obtained from the response by
``` php 
 $user_details= $response->getResponse();
 $email = $user_details['email'];
````


+ Edit  loadUserByOAuthUserResponse() function as given below
``` php
public function loadUserByOAuthUserResponse(UserResponseInterface $response)
{
    $user_details= $response->getResponse();
    $email = $user_details['email'];
     
    return $this->loadUserByUsername($email);

        
    }
```
**3**. To check whether the user exists in the database, edit loadUserByUsername($username) function as given below
``` php
public function loadUserByUsername($username)
{
    //get the entity manager
    $em = CMSBundle::getContainer()->get('doctrine')->getEntityManager();
    
    //load the user from DB
    $entity = $em->getRepository('XYZBundle:User')->loadUserByUsername($username);

    if(!$entity){
        //if the user does not exist, then return a new OAuth user
        $user = new OAuthUser($username);
     } else {
         // if the user exists, return that user
        $user = $entity;
    }
    return $user;
    
}
```