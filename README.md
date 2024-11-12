# fastapi-zitadel-auth

Python code example for FastAPI using Zitadel + Authorization Code Flow with PKCE and JKWS with caching.

Inspired by [Intility/fastapi-azure-auth](https://github.com/Intility/fastapi-azure-auth).

> [!WARNING]
> This repo is a work in progress and should not be used in production just yet.

## Zitadel setup

### Project
* Create a new project. 
* in the General settings, tick "Assert Roles on Authentication" and "Check authorization on Authentication"
* Note the project ID (also called "resource Id") as `ZITADEL_PROJECT_ID`
* Under Roles, create a new role with key: "user" and Display Name "user" and assign it to the project

### App 1: API
* Create a new application in the project of type "API" and Authentication Method "JWT (Private Key JWT)"
* Create a key of type "JSON"

### App 2: User Agent
* Create a new application in the project of type "User Agent" and Authentication Method "PKCE".
* Toggle "Development Mode" to allow non-https redirect URIs
* Under "Redirect URIs", add:
  * `http://localhost:8001/`
  * `http://localhost:8001/oauth2-redirect`
* Token settings
  * Change "Auth Token Type" from "Bearer Token" to "JWT"
  * Tick "Add user roles to the access token"
  * Tick "User roles inside ID token"
* Note the Client Id (as `OAUTH_CLIENT_ID`)

### User creation
* Create a new User in the zitadel instance.
* Under Authorizations, create new authorization by searching for the project name and assign the "user" role to the new user


### Service User creation
* Create a new Service User in the zitadel instance and select the Access Token Type to be "JWT".
* Under Authorizations, create new authorization by searching for the project name and assign the "user" role to the new service user
* Under Keys, create a new key of type "JSON" and note the key ID and download the key (JSON file).
* You can now use this key's data to authenticate the service user to the API, 
see the `service_user.py` file.


## FastAPI setup

Copy the `.env.example` file to `.env` and fill in the values above.

### Run with [uv](https://docs.astral.sh/uv/)

```bash
uv run main.py
```

### Alternatively, use classic venv:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python main.py
```

### Swagger UI

Open http://localhost:8001/docs in a new browser window, click on the "Authorize" button, 
log in, and then access the `/protected` endpoint in the Swagger UI.


### Service User

While the server is running, in another terminal, run the `service_user.py` script to authenticate the service user.
Make sure to have replaced the json_data key with the data from the service user key JSON file.

```bash
uv run service_user.py
```

