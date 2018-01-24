## Message Attachments {#message-attachments}

{% method %}

Messages can also contain attachments - which are formatted as a list view within the main message. All fields are optional. Attachments contain the following fields:

| Field\(All optional\) | Description |
| :--- | :--- |
| title | The title of the attachment |
| title\_link | The URL that the user will be taken to on clicking the title |
| text | The text of the attachment |
| image\_url | The URL of the image for this attachment |
| thumb\_url | The URL of the thumbnail of the attachment |
| color | The color of the attachment text |
| author\_name | The name of the author \(if referenced in the attachment\) |
| author\_icon | The icon URL representing the author \(if referenced in the attachment\) |
| author\_link | The link to the author \(if referenced in the attachment\) |
| footer | Footer for the attachment |
| footer\_icon | The icon URL representing the footer information |
| pretext | A fallback text before the attachment content |
| ts | A Unix timestamp for the message, shown after the footer |
| **fields** | An array of fields to be shown below the attachment |
| **buttons** | An array of buttons to be showed below the attachment |

  
{% sample lang="py" %}

```py
Sending a message with attachment response to an API URL call


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

        attachment_1 = MessageAttachmentsClass()
        attachment_1.title = "This is first Attachment Title"
        attachment_1.title_link = "https://http://www.github.com/lord/slate/"
        attachment_1.text = "This is an attachment text"
        message.attach(attachment_1)

        attachment_2 = MessageAttachmentsClass()
        attachment_2.title = "This is second Attachment Title"
        attachment_2.text = "This is the attachment text for the second attachment"
        message.attach(attachment_2)


        # Returning message response
        return HttpResponse(message.to_json())
        # Alternatively, you can send a simple JSON response like
        # return HttpResponse(json.dumps({"message_text":"I have received your command!", "attachments":[{"title":"This is first Attachment Title", "title_link":"https://http://www.github.com/lord/slate/", "text": "This is an attachment text"},{"title":"This is second Attachment Title", "text": "This is the attachment text for the second attachment"}]}))

      else:
        # Handling incorrect verification token
        error_message = {"message_text":"Incorrect Verification token"}
        return HttpResponse(json.dumps(error_message), content_type="application/json")

```

{% endmethod %}



### Attachment Fields

{% method %}

Messages can also contain attachments - which are formatted as a list view within the main message. All fields are optional. Attachments contain the following fields:

| Field\(All optional\) | Description |
| :--- | :--- |
| title | The title of the attachment |
| title\_link | The URL that the user will be taken to on clicking the title |
| text | The text of the attachment |
| image\_url | The URL of the image for this attachment |
| thumb\_url | The URL of the thumbnail of the attachment |
| color | The color of the attachment text |
| author\_name | The name of the author \(if referenced in the attachment\) |
| author\_icon | The icon URL representing the author \(if referenced in the attachment\) |
| author\_link | The link to the author \(if referenced in the attachment\) |
| footer | Footer for the attachment |
| footer\_icon | The icon URL representing the footer information |
| pretext | A fallback text before the attachment content |
| ts | A Unix timestamp for the message, shown after the footer |
| **fields** | An array of fields to be shown below the attachment |
| **buttons** | An array of buttons to be showed below the attachment |



{% endmethod %}  




