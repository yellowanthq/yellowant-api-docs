# Webhook messages

YellowAnt allows you to send messages to the user through webhook functions in case of new updates or events. The difference between simple message and webhook message is that with webhook message, the user can get message, and also use the data that comes along with the message in Event workflows.

First go to the the application page in your Developer Console. Click on "Create a function". In the new function page, select the function type as "Webhook". Also make sure that you upload the schema of the JSON data to the Webhook function's **Output Keys**.

`POST https://api.yellowant.com/api/user/application/webhook/<webhook-name>/`

| Field | Description |
| :--- | :--- |
| message\_text | The text of the message |
| requester\_application | The Id of the user integration to send this message to \(that you acquired during[create user integration](https://yellowant.com/api/#create-a-user-integration)as**user\_integration**\) |
| **attachments** | The attachments for the message \(Optional\) |
| **data** | The data along with the message. The output schema must be defined in developer application page for the webhook function \(Optional\) |

```python
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
message.data = {"pull_request_id":"b547gf9ebwrioufb3","repository":"ya-main", "time":19867384, "status":"pending"}

#Sending message to YellowAnt user integration
send_message = yellowant_user_integration_object.create_webhook_message(requester_application = user_integration_object.user_integration_id, webhook_name = "some_webhook_function_invoke_name",**message.get_dict())
```

