# Sending a Simple Message

{% method %}

`POST https://api.yellowant.com/api/user/message/`



YellowAnt allows you to send simple messages to the user in case of new updates or events.

| Field | Description |
| :--- | :--- |
| message\_text | The text of the message |
| requester\_application | The Id of the user integration to send this message to \(that you acquired during[create user integration](https://yellowant.com/api/#create-a-user-integration)as**user\_integration**\) |
| **attachments** | The attachments for the message \(Optional\) |



{% sample lang="py" %}

```py
import json
from yellowant.messageformat import MessageClass, MessageAttachmentsClass, MessageButtonsClass
from yellowant import YellowAnt

#Fetching user details and toke from DB
user_obj = FictionalUserDB.objects.get(user_id=435)
user_token = user_obj.yellowant_access_token

#Initializing the YellowAnt User integration object with token acquired during authentication (OAuth2)
user_integration_object = YellowAnt(access_token = user_token)

#Creating a message
message = MessageClass()
message.message_text = "There is new pull request in repo argus/bolo from Perry Johnson"

#adding an attachment for details
attachment = MessageAttachmentsClass
attachment.title = "Pull Request Details"

#adding created at field
field_created = AttachmentFieldsClass()
field_created.title = "Created"
field_created.value = "2 hours ago"
attachment.attach_field(field_created)

#adding status at field
field_status = AttachmentFieldsClass()
field_status.title = "Status"
field_status.value = "Pending"
attachment.attach_field(field_status)

message.attach(attachment)

#Sending message to YellowAnt user integration
send_message = yellowant_user_integration_object.add_message(requester_application = user_integration_object.user_integration_id, **message.get_dict())

```

{% endmethod %}

