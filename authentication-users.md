# User Authentication {#user-authentication}

YellowAnt uses the OAuth 2.0 Authentication protocol which allows you to get a YellowAnt user’s information through a token issued to your application.

You need to first register your application on the YellowAnt Developer Platform. The application is assigned a Client ID and Client Secret, which you will use to get a user’s token. You need to also provide a redirect\_url where the user will be redirected to after authentication - more on that below.

YellowAnt OAuth works in 2 easy steps:

### 1. Authorization {#1-authorization}

Redirect your users to the following URLs

`GET https://www.yellowant.com/api/oauth2/authorize/`

