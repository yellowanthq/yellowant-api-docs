# User

## Get User Profile {#get-user-profile}

{% method %}

Returns the profile details for the authenticated user.

`POST https://api.yellowant.com/api/user/profile/`



{% sample lang="py" }

```py

yellowant_user = YellowAnt(access_token='sdfjbr32p89pdgDFF4p27cQd278p2DWcnp497f')
profile = yellowant_user.get_user_profile()

user_id = profile['id']
user_first_name = profile['first_name']
user_last_name = profile['last_name']
```

```py
The endpoint returns the following JSON data


{
  "id": 432,
  "username": "h74d-r34f-4dsf-2drw-4r2ed",
  "first_name": "Howard",
  "last_name": "Sanchez",
  "is_active": true,
  "email": "howards@rm.com",
  "profile": {
    "nickname": "Hodor",
    "tz_string": "Asia/Kolkata",
    "tz_offset": 19800,
    "language": "en"
  }
}
```

{% endmethod %}

