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

Example:

```
    action.user.create:
        parent: core.action.abstract
        class: Requestum\ApiBundle\Action\CreateAction
        arguments:
            - AppBundle\Entity\User
            - AppBundle\Form\User\UserType
```


####Event listeners
You can create listeners that can work before and after save entity.
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

####Transactions
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