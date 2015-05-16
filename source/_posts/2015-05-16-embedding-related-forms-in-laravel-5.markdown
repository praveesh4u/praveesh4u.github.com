---
layout: post
title: "Embedding related forms in Laravel 5"
date: 2015-05-16 20:43:52 +0530
comments: true
categories:
- Laravel 5
- Forms
---
Sometimes, there arises the need for embedding forms for creating more than one type of entities in a single form. Most of the time the two entities have a parent-child relation and to make things more complicated, dynamic addition and deletion of the child entity form will also be a part of the requirement. Symfony has functionality for implementing such forms built in within the framework which saves a lot of typing and time. Although Laravel 5's form implementation is relatively flexible compared to Symfony, there is no out of the box solution for embedding forms. After Googling for a decent amount of time and a Stackoverflow question that earned me a "[Tumbleweed](http://stackoverflow.com/help/badges/63/tumbleweed)" badge, I decided to get my hands dirty. This may not be the most elegant solution but I can assure you that this works. I have tried to take advantage of cool laravel features like HTML macros so that the solution is more Laravel-ish.

The problem scenario in my case: I have a client entity which have one to many relation with a client user entity. That is, one client can have more than one client users. The form for adding a client should embed the form for adding the associated client users too. By default when the form loads, it should have form for adding one user. There should be a button for populating more client user forms in case a client has more than one form. Yeah, you guessed it right, we have to use Javascript.

![This is how the final form should look like](https://lh3.googleusercontent.com/YRku4651uAsz5VDDCrWtEtIK16iEB1nmAMU-bzS3UsQo0Q90uUE-NDFKJ8LqJDZTRCdquQuuZV7sD6M=w1896-h875-rw "Final form")
