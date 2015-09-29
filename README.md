# Rose-Hulman Firebase Authentication

This is a simple service that authenticates Rose-Hulman students via Kerberos Login and returns a [Firebase Custom Auth Token](https://www.firebase.com/docs/web/guide/login/custom.html).

This README and documentation is a work in progress

## Production Use

Tokens recieved from this service will have the following fields, which can accessed in your firebase rules. More information about this can be found in [Firebase's Custom Auth Documentation](https://www.firebase.com/docs/web/guide/login/custom.html).

```json
uid: (String) the Rose username of the user
email: (String) the email of the user
domain: (String) "rose-hulman.edu"
timestamp: (String) an ISO formatted timestamp of when it was created.
```

### Endpoints

The hostname for this endpoint has yet to be determined, but will be behind Rose-Hulman's Firewall. These endpoints will also eventually support [jsonp](https://en.wikipedia.org/wiki/JSONP) requests for javascript clients.

#### POST `/api/register/`

A post request to `/api/register/` requires the following parameters in the JSON body of the request. The email and password is who the project will be registered under. 

```json
{
  "email": "rockwotj@rose-hulman.edu",
  "password": "Pas$w0rd", 
  "secret": "<FIREBASE SECRET HERE>"
}
```

This will then return a JSON object like the following.

```json
{
  "username": "rockwotj",
  "timestamp": "<ISO formatted timestamp>", 
  "registryToken": "<YOUR REGISTRY TOKEN HERE>"
}
```

Note: When the person who the project is registered under is authenticated via the `/api/auth/` endpoint, they have full read-write access to the entire firebase repo by default. This can be disabled by explictly setting admin to false in the `/api/auth/` endpoint.

#### POST `/api/auth/`

A post request to `/api/auth/` requires the following parameters in the JSON body of the request. The options field and all of that object's fields are completely optional and not required.

```json
{
  "email": "user@rose-hulman.edu",
  "password": "Pas$w0rd", 
  "registryToken": "<REGISTRY TOKEN HERE>",
  "options": {
    "expires": 12340192830,
    "notBefore": 1234234134,
    "admin": false
  }
}
```

These are the options for the endpoint, more information can be found [here](https://github.com/firebase/firebase-token-generator-node#token-options).

<b>expires</b>: (Integer) A timestamp of when the token is invalid.

<b>notBefore</b>: (Integer) A timestamp of when the token should start being valid.

<b>admin</b>: (Boolean) If true, then all security rules are disabled for this user.

This endpoint returns an object that looks like this

```json
{
  "token": "<Auth token for user@rose-hulman.edu>",
  "timestamp": "<ISO formatted timestamp>",
  "username": "user"
}
```

#### Endpoint Errors

If there is an error during a request for any endpoint then the response will look like this

```json
{
  "error": "error message",
  "status": "HTTP Status code of the error"
}
```

### Client Libraries

In the near future there will be client libraries available to more easily integrate this into your code.

#### Java

TODO

#### Swift

TODO

#### Javascript

TODO


## Production Setup

This is a simple nodejs app that is reverse proxied by nginx. Here are the deployment instructions:

```
git clone https://github.com/rockwotj/rose-firebase-auth.git/
npm install
./scripts/start
```

Make sure nginx is set up over HTTPS to proxy to localhost:8080. The nginx configuration is included below.

### Secrets File

In order to run this server, a `secrets.json` file is required. At minimum, it must have a 'key' field that will be used as a symmetric key for the JWT that is the registry token. It may also include the following fields: 'subject', 'issuer', and 'audience'.

### Nginx Configuration 

TODO

