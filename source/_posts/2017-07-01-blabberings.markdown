---
layout: post
title: "Laravel Polymorphic relations"
date: 2017-07-01 17:57:23 +0530
comments: true
categories: PHP, Laravel, Eloquent
---

Let's see how we can customize Eloquent's polymorphic. The default documentation is not quite clear about how you can use regular column names instead of weird column names like "commentable", "postable" etc.

##Problem statement

We have two Models for storing the Address and Customer details of a firm. An Address may be associated with a person or a company as these two are subsets of the firm's customers. A Customer can have more than one address associated with them. Since the customer can be either a company or a person, we can represent this scenario using a polymorphic relation between Customer, Company and Address.

On Address model we will have a column to store the customer's id and another colum to represent whether the customer is a company or a person.

Let's consider the following schema:
``` php Basic Schema
 id
 customer_type
 customer_id
```
#####On Person Model

``` php Person.php
public function address()
{
    return $this->morphMany('App\Models\Address',null,'customer_type','customer_id','id');
}
```

#####On Company Model
``` php Company.php
public function address()
{
    return $this->morphMany('App\Models\Address',null,'customer_type','customer_id','id');
}
```


#####On Address Model
``` php Address.php
public function customer()
{
    return $this->morphTo('customer','customer_type');
}
```
The arguments passed to the morphTo method has to match the column name for the foreign key on the model that is common to the other two models. Conside the following case,
```
$this->morphTo('xxxx','yyyyy');
```
In this case, there should be a column on the Address modal with name ```xxxx_id``` to represent the foreign key of the related model on the shared model table and there should be a column named ```yyyyy``` on the shared model to represnt the type. That is column ```yyyyy``` on address table should represnt whether the id on the xxxx_id column represents a company or a customer.

###Saving relations - Adding a new address to a customer

```php  Saving
$user = User::find(1);
$address = new Address();
$address->address = 'Test';
$user->address()->save($address);
```


###Saving relations - Associating a customer with an address

``` php Saving
$address = new Address();
$address = User::find(1);
$address->address = "Test";
$address->customer()->associate($user);
$address->save();
```




