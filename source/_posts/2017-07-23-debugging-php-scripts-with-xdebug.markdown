---
layout: post
title: "Debugging PHP Scripts with Xdebug"
date: 2017-07-23 19:50:08 +0530
comments: true
categories: PHP, Xdebug, Debugging
---

This is just a small post meant for all those poor souls like me who couldn't get xdebug configured as easy as it should be. The steps are quite straight forward but still I got cought up in "the set up" for nearly 3hours. 
### Environment
  - Mac OS X Sierra
  - PHP 7.1
  - PHPStorm 

I am assuming that you have homebrew installed on your mac.

Step 1: Run ``` brew doctor ``` in the terminal and follow the onscreen instructions to fix issues with homebrew if any.

Step 2: To install xdebug, run ```brew install phpXX-xdebug``` where XX stands for the PHP version that you are using. For example, if you are using PHP 7.1, run `brew install php71-xdebug` and if you are using PHP 7.0, run `brew install php70-xdebug`.

Step 3: Navigate to `/usr/local/etc/php/7.1/conf.d` and add the following lines to `ext-xdebug.ini`.
    {% codeblock lang:php /usr/local/etc/php/7.1/conf.d/ext-xdebug.ini %}
      [xdebug]
      zend_extension="/usr/local/opt/php71-xdebug/xdebug.so"
      xdebug.remote_connect_back = 1
      xdebug.idekey = PHPSTORM
      xdebug.default_enable = 1
      xdebug.remote_mode=req
      xdebug.remote_host = 127.0.0.1
      xdebug.remote_enable = 1
      xdebug.remote_port = 9333
      xdebug.remote_handler = dbgp
      xdebug.profiler_enable=0
      xdebug.profiler_enable_trigger=1
      xdebug.remote_autostart=1
      xdebug.idekey=PHPSTORM
      ; xdebug.remote_log=/usr/local/etc/php/7.1/xdebug.log
    {% endcodeblock %}

Step 4: The first line in the file
`zend_extension="/usr/local/opt/php71-xdebug/xdebug.so"` will be already present in the file. Please add this if not already present.

Step 5: By default, xdebug runns on port 9000. I have changed it to 9333 as I have other stuff that runs on port 9000

Step 6: You can monitor the xdebug logs by uncommenting this line
`; xdebug.remote_log=/usr/local/etc/php/7.1/xdebug.log`.
Pleasee note that this file can get really really big over the time. So, once you have got xdebug up and running, it is a good idea to stop the log.

Step 7: In PHP storm, under preferences, navigate to *Languages & Frameworks > PHP > Debug* and set the following parameters

###### External connections
  - Detect path mappings from deployment configurations
  - Break at first line in PHP Scripts
###### Xdebug
  - Debug port 9333(the same port nummber that we give in step 5)
  - Check 'Can accept external connections'
  - Check 'Force break at the first line when no path mapping specified'
  - Check 'Force break at the first line when a script is outside the project'
  <iframe src="https://drive.google.com/file/d/0B79rmAsCK2UJdEIxLVoxUFFlcVU/preview" width="640" height="480"></iframe> 
Step 8:  In PHP storm, under preferences, navigate to *Languages & Frameworks > PHP > Servers * create a configuration for the local server and add xdebug as the debugger.

  <iframe src="https://drive.google.com/file/d/0B79rmAsCK2UJU2JWTXVvSTRpdjg/preview" width="640" height="480"></iframe>

Step 9: Once you have configured the server, you would need to create a debug configuration. For this navigate to Edit configurations under Run menu and add a new configuration based on PHP Remote debug. For this, click on the + icon on the top left corner and select the server created in step 8 as the server and give the value of xdebug.idekey in step 3 as the Ide Key and give a suitable name to the configuration
  <iframe src="https://drive.google.com/file/d/0B79rmAsCK2UJNFV3UlktdHc0RFk/preview" width="640" height="480"></iframe>

Step 10: Now, select the debug configuration and start listening for the debug connections by clicking on the receiver icon in PHPStorm. Once you have done this, the debug window will open inside php when your script is run.

  <iframe src="https://drive.google.com/file/d/0B79rmAsCK2UJS0tjdVpmUWpIME0/preview" width="640" height="480"></iframe>







