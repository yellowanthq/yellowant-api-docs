# User Integrations

A user integration is a unique instance of integration of your application by a user.

## Create a User Integration {#create-a-user-integration}

Creates a new user integration for the authenticating application and user.

An application can create multiple user integrations for an authenticated user, so that the YellowAnt user can connect multiple application accounts. If the user already has atleast one integration of the current application, the invoke name will be automatically assiged as`appname-number`,`number`being the least occupied integration number. For example, the first user application will be named`application`\(the default invoke\_name of the application set in the application developer page\), the next user application for the same application will be name`application-2`and so on.

`POST https://api.yellowant.com/api/user/integration/`

#### Query Parameters : None

> **Info** **Please note that the user can, at any point, change the invoke name of the user application, and therefore you should regularly check for any user application invoke\_name\_changes**

```python
yellowant_user = YellowAnt(access_token='sdfjbr32p89pdgDFF4p27cQd278p2DWcnp497f')
profile = yellowant_user.create_user_integration()

user_intergation_id = profile['user_application']
user_intergation_invoke_name = profile['user_intergation_invoke_name']
```

```text
The endpoint returns the following JSON data

{
  "application": 27,
  "user_invoke_name": "github",
  "user_application": 132
}
```

## Delete a User Integration {#delete-a-user-integration}

Deletes a user integration for the authenticating application and user.

`DELETE https://api.yellowant.com/api/user/integration/<user-integration-id>/`

#### Query Parameters : None

```python
yellowant_user = YellowAnt(access_token='sdfjbr32p89pdgDFF4p27cQd278p2DWcnp497f')
user_integration_id = 35245
profile = yellowant_user.delete_user_integration(id=user_integration_id)
```

```text
The endpoint returns a HTTP 204 status with JSON response

{"message": "Successfully deleted"}
```

## Update a User Integration {#update-a-user-integration}

Updates a user integration for the authenticating application and user with the supplied user\_invoke\_name. In case of the user\_invoke\_name is invalid or is already being used by another user application, the endpoint will return a HTTP\_406 error.

`PATCH https://api.yellowant.com/api/user/integration/<user-integration-id>/`

### Query Parameters : {#query-parameters}

| Parameter | Description |
| :--- | :--- |
| user\_invoke\_name | The new user invoke name for the user application |

```python
yellowant_user = YellowAnt(access_token='sdfjbr32p89pdgDFF4p27cQd278p2DWcnp497f')
user_integration_id = 27
profile = yellowant_user.update_user_integration(id=user_integration_id, user_invoke_name="github-vader")
```

```text
On successful update, the endpoint returns a HTTP 204 status with JSON response

{
  "application": 27,
  "user_invoke_name": "github-vader",
  "user_application": 133
}
```

