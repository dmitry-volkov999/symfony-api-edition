Docs
--------
/api/doc

Authentication
--------------
Api uses OAuth2 authentication. To authenticate your request add "Authorization" header with value "Bearer access_token"

Fixtures
--------
**User 1**\
   username: artur@gmail.com\
   password: 123

**User 2**\
   username: kirill@gmail.com\
   password: 123
   
**Access Tokens:** Access_Token_For_Artur, Access_Token_For_Kirill

Create Action
-------------

There are two required parameters: Entity class and FormType Class.
Example:

```
# src/AppBundle/Resources/config/services.yml

services:
    #...

    action.user.create:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\CreateAction
        arguments:
            - AppBundle\Entity\User
            - AppBundle\Form\User\UserType
```


## Available Options

 | Option                | Type      | Description                                              |
 | ----------------------| --------  |----------------------------------------------------------|
 | http_method           | string    |HTTP method                                               |
 | success_status_code   | integer   |Status that is returned after execution                   |
 | return_entity         | boolean   |Result entity in response                                 |
 | form_options          | array     |options that will be used in building form                |
 | before_save_events    | array     |Before submit events (events that throws before the flush)|
 | after_save_events     | array     |After submit events (events that throws after the flush)  |
 | access_attribute      | string    |Access Attribute                                          |

## Default Options

 | Option                | Value     |
 | ----------------------| --------  |
 | success_status_code   | 201       |
 | access_attribute      | create    |
 | http_method           | POST      |
 | return_entity         | true      |


## Event listeners

You can create listeners that will respond to event occuring before and after submit the request.
You need to configure it in `services.yml` file:
```
    before_save.user.event:
        class: Requestum\ApiBundle\EventListener\UserBeforeSaveListener
        arguments: ["@security.token_storage"]
        tags:
            - { name: kernel.event_listener, event: action.before_save_user, method: onBeforeSaveUser }

    after_save.user.event:
        class: Requestum\ApiBundle\EventListener\UserAfterSaveListener
        arguments: ["@security.token_storage"]
        tags:
            - { name: kernel.event_listener, event: action.after_save_user, method: onAfterSaveUser }

```
Then you need to specify this listeners in create action configuration:
```
    action.user.create:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\CreateAction
        arguments:
            - AppBundle\Entity\User
            - AppBundle\Form\User\UserType
        calls:
            - ['setOptions', [{'before_save_events': ['action.before_save_user'], 'after_save_events': ['action.after_save_user']}]]
```


Update Action
-------------

There are two required parameters: Entity class and FormType Class.
Example:

```
# src/AppBundle/Resources/config/services.yml

services:
    #...

    action.user.update:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\UpdateAction
        arguments:
            - AppBundle\Entity\User
            - AppBundle\Form\User\UserType
```


## Features and Options

## Default Options

  | Option                | Value     |
  | ----------------------| --------  |
  | success_status_code   | 200       |
  | access_attribute      | update    |
  | http_method           | PATCH     |
  | return_entity         | true      |

Update action has the same available features and options as a create action. (see "Create Action")



Delete Action
-------------

There is one required parameter: Entity class.
Example:

```
# src/AppBundle/Resources/config/services.yml

services:
    #...

    action.user.delete:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\DeleteAction
        arguments:
            - AppBundle\Entity\User
```


## Options

 | Option                | Type         | Description                                            |
 | ----------------------| -------------|--------------------------------------------------------|
 | fetch_field           | string       |The field that is the entity identifier (id by default) |
 | before_delete_events  | array        |Before delete events                                    |
 | access_attribute      | string       |Access Attribute                                        |

## Default Options

  | Option                | Value     |
  | ----------------------| --------  |
  | fetch_field           | id        |
  | access_attribute      | delete    |
  | http_method           | PATCH     |
  | return_entity         | true      |

## Event listeners

You can create listeners that will respond to event occuring before delete the entity.
You need to configure it in `services.yml` file:
```
    before_delete.user.event:
        class: Requestum\ApiBundle\EventListener\UserBeforeDeleteListener
        tags:
            - { name: kernel.event_listener, event: action.before_delete_user, method: onBeforeDeleteUser }

```
Then you need to specify this listeners in delete action configuration:
```
    action.user.delete:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\DeleteAction
        arguments:
            - AppBundle\Entity\User
        calls:
            - ['setOptions', [{'before_delete_events': ['action.before_delete_user'] }]]
```
