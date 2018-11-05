## Basics

Currently CE leverages two internal PubNub services to handle _granting_ and _revoking_ user access control limits (ACLs) across the various chat channels assigned to CE users.

1. PubNub [Functions](https://www.pubnub.com/tutorials/pubnub-functions/) (i.e. microservice).
2. PubNub Access Monitor (i.e [PAM](https://www.pubnub.com/tutorials/pubnub-access-manager/)).

In short, the communication follows this pattern:

1. CE Client code sends a series of RESTful HTTP calls to a PubNub On Request Function (i.e. a microservice running the CE backend).
2. First, CE tries to connect to default chat channels generated from ```ChatEngine.global```. PubNub CE Function receives a request and makes subsequent ```grant``` calls to PubNub's PAM service (i.e. authorization service). Granting either read and or write access to relevant CE ```ChatEngine.global``` chat channels.
3. Next, CE tries to connect to default chat channels generated from ```ChatEngine.Me```. Similar to step 2, request are routed to the PubNub CE On Request Function, granting read and or write access to the relevent CE ```ChatEngine.Me``` chat channels.
4. After the two connection attempts, if successful, CE emits a ```$.ready``` event signaling CE has successfully initialized and connected to the default ```ChatEngine.global``` and ```ChatEngine.Me``` chat channels.

> A ```secretKey``` is required to grant with PAM. The PubNub CE Function stores an encrypted version of the ```secretKey``` within a secret store (i.e. [PubNub Functions Vault module](https://www.pubnub.com/docs/blocks/vault-module))

## Editing the Default Security Policy

While the default ChatEngine security policy is sufficient to prevent users from accessing other user’s _private_, _feed_ and _direct_ chats, a user might still be able to connect to ChatEngine with a random ```UUID``` and access all [public chats](connect.md#Connect ChatEngine.Global).

If additional security controls are required, it is possible to edit the default security policy by adding logic to the PubNub CE Function. Since ```authKeys``` are generated by your server, a custom policy can be added in the PubNub CE Function to send XHR requests to the server to validate a user ```UUID``` and ```authKey```. If the ```authKey``` is invalid or expired, the PubNub CE Function can block the ```ChatEngine.connect()``` call.

Effectively this provides CE with authentication logic. To read further on this topic, refer to the following documentation on [adding authentication to CE](../advancedConcepts/authentication.md).