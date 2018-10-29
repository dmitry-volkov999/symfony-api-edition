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

Abstract Form Action Class
-------------

This is an abstract class that is the parent for the Create and Update Actions.
Can be used to inherit and to create another custom actions.

#### Available Options

 | Option                | Type      | Description                                              | Default Values    |
 | ----------------------| --------  |----------------------------------------------------------|-------------------|
 | http_method           | string    |HTTP method                                               | POST              |
 | success_status_code   | integer   |Status that is returned after execution                   | 200               |
 | return_entity         | boolean   |Result entity in response                                 | true              |
 | form_options          | array     |options that will be used in building form                | []                |
 | before_save_events    | array     |Before submit events (events that throws before the flush)| []                |
 | after_save_events     | array     |After submit events (events that throws after the flush)  | []                |


Create Action
-------------

Action to create a new object.
This is a subclass that inherits from AbstractFormAction class.

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


#### Available Options

 | Option                | Type      | Description                                              | Default Values    |
 | ----------------------| --------  |----------------------------------------------------------|-------------------|
 | http_method           | string    |HTTP method                                               | POST              |
 | success_status_code   | integer   |Status that is returned after execution                   | 201               |
 | return_entity         | boolean   |Result entity in response                                 | true              |
 | form_options          | array     |options that will be used in building form                | []                |
 | before_save_events    | array     |Before submit events (events that throws before the flush)| []                |
 | after_save_events     | array     |After submit events (events that throws after the flush)  | []                |
 | access_attribute      | string    |Access Attribute                                          | create            |

#### Event listeners

By default Create and Update actions throws such events: `'action.before_save'`, `'action.after_save'`. You can dispatch this events, or throw another events using such options as: `before_save_events` and `after_save_events`.

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
Action to update an existing object.
This is a subclass that inherits from AbstractFormAction class.

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


#### Available Options

 | Option                | Type      | Description                                              | Default Values    |
 | ----------------------| --------  |----------------------------------------------------------|-------------------|
 | http_method           | string    |HTTP method                                               | PATCH             |
 | success_status_code   | integer   |Status that is returned after execution                   | 200               |
 | return_entity         | boolean   |Result entity in response                                 | true              |
 | form_options          | array     |options that will be used in building form                | []                |
 | before_save_events    | array     |Before submit events (events that throws before the flush)| []                |
 | after_save_events     | array     |After submit events (events that throws after the flush)  | []                |
 | access_attribute      | string    |Access Attribute                                          | update            |


Update action has the same available features and options as a create action. (see "Create Action")


Delete Action
-------------
Action to delete an existing object

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


#### Available Options

 | Option                | Type         | Description                                            | Default Values   |
 | ----------------------| -------------|--------------------------------------------------------|------------------|
 | fetch_field           | string       |The field that is the entity identifier (id by default) | id               |
 | before_delete_events  | array        |Before delete events                                    | []               |
 | access_attribute      | string       |Access Attribute                                        | delete           |

#### Event listeners
By default Delete action throws a such event: `'action.before_delete'`. You can dispatch this event, or throw another events using such an option: `before_delete_events`.

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
