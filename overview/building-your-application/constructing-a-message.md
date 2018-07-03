# Constructing a Message

## Constructing a basic Message response {#basic-formatting}

After processing the user command, you must respond with a message. YellowAnt messages consist of upper level formatting options along with attachments, attachment fields and attachment buttons.

![](../../.gitbook/assets/screenshot-api.slack.com-2018-01-28-03-51-25-409%20%281%29.png)

Let’s look at the structure of a YellowAnt message - it consists of the following fields:

| Field | Description |
| :--- | :--- |
| message\_text | The text of the message |
| **attachments** | The attachments for the message \(Optional\) |

{% codetabs name="Python", type="py" -%}
msg = "Hello World"
print msg
{%- language name="JavaScript", type="js" -%}
var msg = "Hello World";
console.log(msg);
{%- language name="HTML", type="html" -%}
<b>Hello World</b>
{%- endcodetabs %}

```python
import json
from yellowant.messageformat import MessageClass, MessageAttachmentsClass, MessageButtonsClass

#Receiving command request from the user in a Django view
def api_url(request):    
      data = json.loads(request.POST["data"])
      args = data["args"]
      service_application = data["application"]
      verification_token = data['verification_token']
      function_id = data['function']
      function_name = data['function_name']

      if verification_token == settings.verification_token:
        # Processing command in some class Command and sending a Message Object
        message = MessageClass()
        message.message_text = "I have received your command!"
        # Returning message response
        return HttpResponse(message.to_json())

        # Alternatively, you can send a simple JSON response like
        # return HttpResponse(json.dumps({"message_text":"I have received your command!"}))

      else:
        # Handling incorrect verification token
        error_message = {"message_text":"Incorrect Verification token"}
        return HttpResponse(json.dumps(error_message), content_type="application/json")
```

