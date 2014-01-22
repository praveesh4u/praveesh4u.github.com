---
layout: post
title: "Symfony 2 Validation groups"
date: 2014-01-22 23:51:28 +0530
comments: true
categories: 
---
When more than one forms are associated with the same entity, the form validator will validate all the properties of that entity for all forms associated with that entity. This would result in form errors as all the forms may not have fields for all the properties of that entity. For example, consider a User entity with username, email address, password, name and phone number as properties. The user needs to enter all these properties when he is initially registering. When the user needs to edit his profile, there he can't edit username and e mail address as these two properties are non-editable. So, the form for editing the profile won't have fields for username and e mail. If the we are using the normal validation, when the user tries to update his profile, the form validation would fail as Symfony would validate the form for username and e mail constraints too. To prevent this, we create validation groups. By creating validation groups, we define separate set of validation constraints for different forms.<br>
Steps:<br>
1. When writing a validation constraint, specify the validation group against it
``` yaml Validations.yml 
username:						
	- NotBlank: ~
	- MinLength: { limit: 5, groups: [ userRegistration] }
address:
	- MinLength: { limit: 10, groups: [ userUpdate] }	
phoneNumber:
	- NotBlank: ~
	- MinLength: { limit: 10 }
	- Type
		type: integer
		message: phone number should contain only numbers	
```

2.Specify the validation group in the form class in the getDefaultOptions function
``` js FormType
public function setDefaultOptions(OptionsResolverInterface $resolver)
{
    $resolver->setDefaults(array(
        'data_class' => 'Venom\BlogBundle\Entity\User',
        'validation_groups' => array('userUpdate', 'userRegistration')
    ));
}
```

3.Pass the validation group when form is created
					
For the user registration action, we create the form with 'userRegistration' validations:
``` js Registration Action
$form = $this->createForm(new UserType(), $entity,array(
                            'validation_groups' => array('userRegistration')));
```
For the user edit action, we create the form with 'userUpdate' validations:
``` js Edit Action
$form = $this->createForm(new UserType(), $entity,array(
                            'validation_groups' => array('userUpdate')));
```

4.If the validation group is not specified when the form is created, the form will be validated for all the default validation constraints(The constraints for which a validation group is not specified)
That is, in our case, a form created with no validation group would get validated only for the phone number field.