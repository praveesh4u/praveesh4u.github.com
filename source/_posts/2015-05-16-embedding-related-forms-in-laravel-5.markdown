---
layout: post
title: "Embedding related forms in Laravel 5"
date: 2015-05-16 20:43:52 +0530
comments: true
categories:
- Laravel 5
- Forms
published: false

---
Sometimes, there arises the need for embedding forms for creating more than one type of entities in a single form. Most of the time the two entities have a parent-child relation and to make things more complicated, dynamic addition and deletion of the child entity form will also be a part of the requirement. Symfony has functionality for implementing such forms built in within the framework which saves a lot of typing and time. Although Laravel 5's form implementation is relatively flexible compared to Symfony, there is no out of the box solution for embedding forms. After Googling for a decent amount of time and a Stackoverflow question that earned me a "[Tumbleweed](http://stackoverflow.com/help/badges/63/tumbleweed)" badge, I decided to get my hands dirty. This may not be the most elegant solution but I can assure you that this works. I have tried to take advantage of cool laravel features like HTML macros so that the solution is more Laravel-ish.

The problem scenario in my case: I have a client entity which have one to many relation with a client user entity. That is, one client can have more than one client users. The form for adding a client should embed the form for adding the associated client users too. By default when the form loads, it should have form for adding one user. There should be a button for populating more client user forms in case a client has more than one form. Yeah, you guessed it right, we have to use Javascript.

{% imgcap https://www.googledrive.com/host/0B79rmAsCK2UJNGEzb2k0SjloMGs The final form %}

A simple solution would be to create a template for the child templates and then append the html for that form to the main form through an AJAX call. Sounds simple but what about form validations? What about form errors ? What about the edit form and the initial page load? To validate the form, we have to consider each child form separately and consider the values in those forms as attributes of the child entity.

####Step 1. Create a form macro amd autoload it using service providers
