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

To enable Create Action, you need to configure it in `services.yml` file.

There are two required parameters: Entity class and FormType Class.
Example:

```
    action.user.create:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\CreateAction
        arguments:
            - AppBundle\Entity\User
            - AppBundle\Form\User\UserType
```


## Options

 | Option                | Type      | Example                                           | Description                                           |
 | ----------------------| --------  |---------------------------------------------------|-------------------------------------------------------|
 | use_lock              | boolean   |`'use_lock': true`                                 |option which gives the opportunity to use transactions |
 | http_method           | string    |`'http_method': PUT`                               |HTTP method                                            |
 | success_status_code   | string    |`'success_status_code' : Response::HTTP_CREATED`   |Status that is returned after execution                |
 | return_entity         | boolean   |`'return_entity' : true`                           |Result entity in response                              |
 | form_options          | array     |                                                   |options that will be used in building form             |
 | before_save_events    | array     |`'before_save_events': ['action.before_save_item']`|Before submit events                                   |
 | after_save_events     | array     |`'after_save_events': ['action.after_save_item']`  |After submit events                                    |
 | access_attribute      | string    |`'access_attribute': 'create'`                     |Access Attribute                                       |


## Event listeners

You can create listeners that will be work before and after save entity.
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

## Transactions

If you need to enable transactions you can do it by configuring create action.
You need to set `true` for option `use_lock`:
```
    action.user.create:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\CreateAction
        arguments:
            - AppBundle\Entity\User
            - AppBundle\Form\User\UserType
        calls:
            - ['setOptions', [{'use_lock': true}]]
```

Update Action
-------------

To enable Update Action, you need to configure it in `services.yml` file.

There are two required parameters: Entity class and FormType Class.
Example:

```
    action.user.create:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\UpdateAction
        arguments:
            - AppBundle\Entity\User
            - AppBundle\Form\User\UserType
```


## Features and Options

Update action has the same features and options as a create action. (see "Create Action")



Delete Action
-------------

To enable Delete Action, you need to configure it in `services.yml` file.

There is one required parameter: Entity class.
Example:

```
    action.user.create:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\CreateAction
        arguments:
            - AppBundle\Entity\User
```


## Options

 | Option                | Type         | Example                                               | Description                                            |
 | ----------------------| -------------|-------------------------------------------------------|--------------------------------------------------------|
 | fetch_field           | string       |`'fetch_field': 'customIdentifierField'`               |The field that is the entity identifier (id by default) |
 | before_delete_events  | array        |`'before_delete_events': ['action.before_delete_item']`|Before delete events                                    |
 | access_attribute      | string       |`'access_attribute': 'delete'`                         |Access Attribute                                        |


## Event listeners

You can create listeners that will be work before delete entity.
You need to configure it in `services.yml` file:
```
    before_delete.user.event:
        class: Requestum\ApiBundle\EventListener\UserBeforeDeleteListener
        tags:
            - { name: kernel.event_listener, event: action.before_delete_user, method: onBeforeDeleteUser }

```
Then you need to specify this listeners in delete action configuration:
```
    action.user.create:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\CreateAction
        arguments:
            - AppBundle\Entity\User
            - AppBundle\Form\User\UserType
        calls:
            - ['setOptions', [{'before_delete_events': ['action.before_delete_user'] }]]
```
