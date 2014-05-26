Secured chat
============
Features
--------
* HTTP REST including authentication and authorization. Keycloak integration.

* Stomp over websockets used for subscription requests. Subscription requests allowed just for authorized users (members of role 'admin' or role 'user').

* Authorized subscription responses received for newly added chat messages and also for deleted or updated messages. Update of messages is not provided by this example, but is possible through Liveoak admin console.

* Users with role _admin_ can delete any chat message. So administrator is allowed to censor everything. Normal users can delete just their own messages.

Steps to run the application
----------------------------
* Copy the example in the _apps_ directory and start Liveoak

	$ cp -r _liveoak-examples_/chat/chat-html-secured _liveoak_/apps
	
	$ sh _liveoak_/bin/standalone.sh

* Create roles for your application (Manual step required).
  * Go to [http://localhost:8080/admin](http://localhost:8080/admin) and login as user "admin" with password "admin".
  * Go to [http://localhost:8080/admin#/applications/chat-html-secured/application-settings](http://localhost:8080/admin#/applications/chat-html-secured/application-settings) and add 2 new roles "admin" and "user". Then you can also select "user" to be default role > Click "Save".
  * Role names are important, because authorization is configured to deal with those 2 roles.

* Add HTML client for newly created application (Manual step currently required)

  * Go to [http://localhost:8080/admin#/applications/chat-html-secured/application-clients](http://localhost:8080/admin#/applications/chat-html-secured/application-clients)
  * Add Client
    * Name: "chat-html-secured-client"
    * Platform: HTML-5
    * Redirect URI: "http://localhost:8080/chat-html-secured/*" (click button "Add")
    * Web Origins: "http://localhost:8080" (click button "Add")
    * Scope: select both "admin" and "user" scopes (if you can't add scopes for the first time, let's create client without scopes and then edit it later and add scopes)
  * Finally click "Save"

* Create some default users for testing purposes (their names and default passwords are not important, feel free to use different names):
  * Go to Keycloak admin console at [http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users](http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users)
  * Add User > username: "bob" > Save
  * Then open [http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users/bob/user-credentials](http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users/bob/user-credentials) and fill some initial password for user "bob". Note that bob will need to change this default password when he try to login for the first time.
  * Then open [http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users/bob/role-mappings](http://localhost:8080/auth/admin/liveoak-admin/console/index.html#/realms/liveoak-apps/users/bob/role-mappings) and select application "chat-html-secured" and move both available roles "admin" and "user" to the assigned roles. This means that bob will be both "admin" and "user" .
  * Repeat the steps and create another user "john", but assign him just to role "user"
  * Repeat the steps again and create last user "mary" and don't assign her to any role

* Open your browser at [http://localhost:8080/chat-html-secured](http://localhost:8080/chat-html-secured)

Users
-----
- User 'bob' with password 'password' is admin and can do anything (subscribe, create new chat messages, view all messages received from subscription, delete any message). He is admin and so he is allowed to censor/delete any message created by any user.

- User 'mary' with password 'password' doesn't have any roles and she can't do anything (view existing messages, create new messages, subscribe to receive messages). She will receive authz error directly
 when you login because she is not authorized to subscribe.

- User 'john' with password 'password' is normal user. He can view existing messages, create new messages and subscribe to receive messages. But he is not authorized
to delete chat messages, which were not created by himself. Basically members of role 'user' can delete just their own messages.


