# User Authentication {#user-authentication}

YellowAnt uses the OAuth 2.0 Authentication protocol which allows you to get a YellowAnt user’s information through a token issued to your application.

You need to first register your application on the YellowAnt Developer Platform. The application is assigned a Client ID and Client Secret, which you will use to get a user’s token. You need to also provide a redirect\_url where the user will be redirected to after authentication - more on that below.

YellowAnt OAuth works in 2 easy steps:

### 1. Authorization {#1-authorization}

Redirect your users to the following URLs

`GET https://www.yellowant.com/api/oauth2/authorize/`

### Query Parameters {#query-parameters}

| Parameter | Description |
| :--- | :--- |
| client\_id | The client Id from your application developer page |
| response\_type | Set value to ‘code’ |
| redirect\_url | The url to redirect the user after authentication |
| state | The state variable to store any user information\(optional\) |

> **Success** **On a successful redirect, the user will be shown a page, where the user can choose a team account to authenticate**


{% method %}


### 2. Get User Token {#2-get-user-token}

Once the user has authenticated, the user will be redirected to the redirect\_url that you set in your application\(and also passed in Step 1\). The redirect url will contain two GET parameters:`code`- A temporary code that will be exchanged for a token`state`- The state variable passed in the URL in Step 1.

`POST https://www.yellowant.com/api/oauth2/token/`

### Query Parameters {#query-parameters}

| Parameter | Description |
| :--- | :--- |
| client\_id | The client Id from your application developer page |
| client\_secret | The client secret from your application developer page |
| grant\_type | Set value to ‘authorization\_code’ |
| redirect\_url | The url to redirect the user after authentication |
| code | The code obtained from the GET parameter |


{% sample lang="py" %}

```py
from yellowant import YellowAnt

code = request.GET.get('code')
CLIENT_ID = 'oLuOCDCScer5oi9GBFWrfewei7238fFWef7c0oGNb6g5Q'
CLIENT_SECRET = '2m89fjXkTnh87d753x4fobFFwf@3netrcSQWSusqrtRu53s'

y = YellowAnt(app_key=settings.CLIENT_ID, app_secret=CLIENT_SECRET,
                  access_token=None,
                  redirect_uri=REDIRECT_URL)
access_token_dict = y.get_access_token(code)
access_token = access_token_dict['access_token']

user_yellowant_object = YellowAnt(access_token=access_token)
```

{% endmethod %}


```
The above command returns JSON structured like this:
```

```py
{
  "access_token": "B69$9v7v6#8ydepiuXheTodDWDc33rVYXEviT4d3d",
  "expires": 31536000,
  "refresh_token": "ei7238fFWef7c0oGNb6gd34n087xb390yx8b4x8",  
}
```

{% method %}
###3. Revoking a user token

`POST https://www.yellowant.com/api/oauth2/revoke_token/`

### Query Parameters {#query-parameters}

| Parameter | Description |
| :--- | :--- |
| token | The token to revoke|


{% sample lang="py" %}
```py
from yellowant import YellowAnt

user_yellowant_object = YellowAnt(access_token=access_token)
user_yellowant_object.revoke_token(token=access_token)


{% endmethod %}