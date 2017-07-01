---
layout: post
title: "Laravel Polymorphic relations"
date: 2017-07-01 17:57:23 +0530
comments: true
categories: PHP, Laravel
---

Let's see how we can customize Eloquent's polymorphic. The default documentation is not quite clear about how you can use regular column names instead of weird column names like "commentable", "postable" etc.

##Problem statement

We have two Models for storing the Address and Customer details of a firm. An Address may be associated with a person or a company as these two are subsets of the firm's customers. A Customer can have more than one address associated with them. Since the customer can be either a company or a person, we can represent this scenario using a polymorphic relation between Customer, Company and Address.

On Address model we will have a column to store the customer's id and another colum to represent whether the customer is a company or a person.

Let's consider the following schema:

1. id
2. customer_type
3. customer_id

#####On Person Model

```
public function address()
    {
        return $this->morphMany('App\Models\Address',null,'customer_type','customer_id','id');
    }
```

#####On Company Model
```
public function address()
    {
        return $this->morphMany('App\Models\Address',null,'customer_type','customer_id','id');
    }
```


#####On Address Model
```
    public function customer()
    {
        return $this->morphTo('customer','customer_type');
    }
````    

###Saving relations - Adding a new address to a customer

```
$user = User::find(1);
$address = new Address();
$address->address = 'Test';
$user->address()->save($address);
```


###Saving relations - Associating a customer with an address

```
$address = new Address();
$address = User::find(1);
$address->address = "Test";
$address->customer()->associate($user);
$address->save();
```




