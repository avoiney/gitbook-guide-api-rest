# OAuth 2 Authorization Code Flow

![OAuth 2 Authorization Code Flow](../../.gitbook/assets/oauth2-authorization-code-flow.png)

1. Le **Client** redirige le **Resource Owner** vers l'**Authorization Server** :

```javascript
https://auth.wishtack.com/v1/oauth/authorize?response_type=code
&client_id=CLIENT_ID
&redirect_uri=https://www.wishtack.com/oauth // optional
&scope=read
&state=... // state is recommended thus optional 😢
```

* **`client_id`** : ID unique du **Client**.
* **`redirect_uri`** : Une des URLs de retour parmi la liste transmise à l'**Authorization Server** à l'enregistrement. Si le paramètre est absent, l'**Authorization Server** utilisera la valeur par défaut configurée lors de l'enregistrement.
* **`scope`** : ID unique du Client.
* **`state`** : Paramètre malheureusement optionnel permettant au **Client** de retrouver le contexte d'initiation de la demande. Il sert surtout à transmettre un "nonce" _\(token aléatoire\)_ pour des raisons de sécurité. 

2. Le **Resource Owner** confirme ou rejette les autorisations d’accès demandées sur l’interface proposée par l'**Authorization Server**.

3. Le **Client** reçoit l'**Authorization Code** par redirection _\(paramètre `code`\)_ :

```javascript
https://www.wishtack.com/oauth?code=AUTHORIZATION_CODE&state=...
```

4. Le **Client** peut alors échanger l'**Authorization Code** contre un **Access Token** auprès de l’API OAuth 2 de l'**Authorization Server**.

```javascript
POST https://auth.wishtack.com/v1/oauth/token

client_id=CLIENT_ID
&client_secret=CLIENT_SECRET
&grant_type=authorization_code
&code=AUTHORIZATION_CODE
&redirect_uri=https://www.wishtack.com/oauth
```

* **`client_secret`** : Secret du **Client** configuré lors de l'enregistrement.

5. En cas de succès, le **Client** reçoit alors l'**Access Token** et un **Refresh Token** optionnel :

```javascript
{
    "access_token": "ACCESS_TOKEN",
    "token_type": "bearer",
    "expires_in": 2592000,
    "refresh_token": "REFRESH_TOKEN",
    "scope": "read",
    "wishtack_user_data":{
        "first_name": "John",
        "last_name": "DOE",
        "email": "j.doe@ibm.com",
        "is_cool": "definitely not!"
    }
}
```

En cas d’expiration de l'**Access Token** et si le **Client** a reçu un **Refresh Token**, le **Client** peut renouveler sa demande avec le **Refresh Token** et obtenir de nouveaux **Access Token** et **Refresh Token**.

L'**Authorization Code** est un code à usage unique dont la durée de vie doit être très courte _**\(moins de 10 minutes\)**_.

