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
| title | The Title of the Field |
| value | The value of the Field |
| short | 0 if each field must occupy a row, 1 - if multiple fields can be displayed per row |

{% sample lang="py" %}

```
tasks = [{
  "title":"Bundle middleman error fix",
  "body": "Getting this error when running bundle exec middleman server",
  "status":"open",
  "project":"PBL-1",
  "initiator":{
    "name":"Boo-Boo Bear",
    "id":"463",
    "url":"https://www.bitbucket.org/booboo/",
    "image_url":"https://www.jellystone.com/park/bears/booboo.jpg"
  },
  "priority":"high"
},{
  "title":"Dev Branch: Links to anchor tags load H1 rather than target link",
  "body": "I recently added static TOC/navigation to my flavor of slate and now, when an H2/H3/H4 link is loaded, it loads to the H1 anchor rather than the appropriate anchor linked.",
  "status":"open",
  "project":"PBL-1",
  "initiator":{
    "name":"Yogi Bear",
    "id":"461",
    "url":"https://www.bitbucket.org/yogi/",
    "image_url":"https://www.jellystone.com/park/bears/yogi.jpg"
  },
  "priority":"low"
}
]


message = MessageClass()
message.title = "Open JIRA tickets"
message.message_text = "Following are the open JIRA tickets for project PBL-1"

for task in tasks:
    attachment = MessageAttachmentsClass()
    attachment.title = task['title']
    attachment.author_name = task['initiator']['name']
    attachment.author_link = task['initiator']['url']
    attachment.author_icon = task['initiator']['image_url']

    field_status = AttachmentFieldsClass()
    field_status.title = "Status"
    field_status.value = task['status']
    attachment.attach_field(field_status)

    field_priority = AttachmentFieldsClass()
    field_priority.title = "Status"
    field_priority.value = task['status']
    attachment.attach_field(field_priority)

    message.attach(attachment)

    return message.to_json()
```


{% endmethod %}  


## Attachment Buttons {#attachment-buttons}

{% method %}

Message attachments can also contain interactive buttons - which are defined as an array, and displayed in a row at the bottom of the attachment area

| Field | Description |
| :--- | :--- |
| text | The text displayed opn the button |
| value | The value associated with the button |
| name | A value that helps you categorize each button into groups |
| **command** | The command object in JSON format |

### Attachment Button Commands {#attachment-button-commands}

YellowAnt enables you to encode a command of your application or another third party user application\(if the user has authorized you to encode the third party application’s commands in your application\) inside a button. This command will point to one of your declared functions with arguments to supply to the function. When the user clicks on the button, YellowAnt will send the command object to your API URL\(or to the third party application\) and you can handle it in the same way you handle a regular command. You can also ask the user for input values for the command, which will be rendered as input boxes in a Dialog

The Command Object has the following format:

| Field | Description |
| :--- | :--- |
| service\_application | The user integration id of the application you want the button to call |
| function\_name | The name of the function |
| data | A JSON representation of the function arguments. For example:`{"repo_id":"skywalker", "issue_id":"469"}` |
| inputs | A list of arguments to ask the user as inputs within a Dialog. For example, if you want to show a dialog to get inputs for sending a mail, the inputs value will be:`["to","body", "subject"]` |

{% common %}

```
Following is an example of a task management application listing details of open task tickets and providing a button to close the task for every listed task
```

{% sample lang="py" %}

```py
import json
from yellowant.messageformat import MessageClass, MessageAttachmentsClass, MessageButtonsClass

#Importing a fictional command processor, which will process the command
from project import command_processor

#Receiving command request from the user in a Django view
def api_url(request):    
      data = json.loads(request.POST["data"])
      args = data["args"]
      service_application = data["application"]
      verification_token = data['verification_token']
      function_id = data['function']
      function_name = data['function_name']

      if verification_token == settings.verification_token:


        if function_name == "get_open_tasks":
          project_id = args['project']
          # Processing command in a fictional command_processor class sending a Message Object
          tasks = command_processor.get_open_tasks(service_application, project_id)

          # The above function will return the following JSON Object of all open tasks
          """
          tasks = [{
            "title":"Ability to have nested file structure ",
            "body": "We need the ability to retain our repository's nested markdown file structure. Users may either read documentation in our repository or on the doc site.",
            "status":"open",
            "project":"PBL-1",
            "initiator":{
              "name":"Dick Dastardly",
              "id":"460",
              "url":"https://www.bitbucket.org/dd/",
              "image_url":"https://www.laffalympics.com/dd.jpg"              
            },
            "priority":"high"
          },{
            "title":"Non Latin support",
            "body": "It appears Lunr would need a new locale file for each language we want to support. Is that correct? Just checked their documentation but could use some guidance to get started",
            "project":"PBL-1",
            "status":"open",
            "initiator":{
              "name":"Muttley Mutt",
              "id":"467",
              "url":"https://www.bitbucket.org/muttley/",
              "image_url":"https://www.laffalympics.com/muttley.jpg"
            },
            "priority":"low"
          }]
          """

          message = MessageClass()
          message.title = "Open JIRA tickets"
          message.message_text = "Following are the open JIRA tickets for project PBL-1"

          for task in tasks:
            attachment = MessageAttachmentsClass()
            attachment.title = task['title']
            attachment.author_name = task['initiator']['name']
            attachment.author_link = task['initiator']['url']
            attachment.author_icon = task['initiator']['image_url']

            field_status = AttachmentFieldsClass()
            field_status.title = "Status"
            field_status.value = task['status']
            attachment.attach_field(field_status)

            field_priority = AttachmentFieldsClass()
            field_priority.title = "Status"
            field_priority.value = task['status']
            attachment.attach_field(field_priority)

            #Clicking on this button will call the close_task() function() on the application
            button_status_close = MessageButtonsClass()
            button_status_close.text = "Close Ticket"
            button_status_close.value = "close"
            button_status_close.name = "close"    

            #We now associate the command to a command object. 
            #The 'service_application' value is the Id of the user integration you want the button to call. This can be the current user integration that is sending you this command, which we have already captured in the 'service_application' variable above. 
            #The 'service_application' can also be another user integration from a third party application, provided the user has given your application the permission to access the user's third party application. For example, the current taks management application can also add a button to one of the user's Email application integration like GMail. More on third party app permissions below
            #The 'function_name' is the name of the function you want to call. You must define the function in the Application's Developer page.
            #The 'data' is a dictionary of arguments to pass to the application function

            button_status_close.command = {"service_application": self.user_integration, "function_name": "close_ticket", 
                                      "data": {"id":task['id'], "project": task.project}
                                      }
            attachment.attach_button(button_status_close)
            
            #Clicking on this button will call the add_comment() function() on the application and prompt the user for the "comment" value
            button_ticket_comment = MessageButtonsClass()
            button_ticket_comment.text = "Add a comment"
            button_ticket_comment.value = "comment"
            button_ticket_comment.name = "comment"    

            #We now associate the command to a command object. 
            #The 'service_application' value is the Id of the user integration you want the button to call. This can be the current user integration that is sending you this command, which we have already captured in the 'service_application' variable above. 
            #The 'service_application' can also be another user integration from a third party application, provided the user has given your application the permission to access the user's third party application. For example, the current taks management application can also add a button to one of the user's Email application integration like GMail. More on third party app permissions below
            #The 'function_name' is the name of the function you want to call. In the case, we will call the add_comment function
            #The 'data' is a dictionary of arguments to pass to the application function
            #The 'inputs' is a list of input arguments to ask the user. This will be displayed in a Dialog with input forms. The values entered by the user will then be sent along with the data in the "data" values to the function_name function


            button_status_close.command = {"service_application": self.user_integration, "function_name": "add_comment", 
                                      "data": {"id":task['id'], "project": task.project},
                                      "inputs":["comment"]
                                      }
            attachment.attach_button(button_status_close)



          message.attach(attachment)

          return message.to_json()

        #Handling the case where the command is 'close_task', and the arguments are project and id
        elif if function_name == "close_task":
          task_id = args['id']
          task_project = args['project']
          # Processing command in a fictional command_processor class sending a Message Object
          tasks = command_processor.close_task(service_application, task_project, task_id)
          m = MessageClass()
          m.message_Text = "The ticket is successully closed!"


      else:
        # Handling incorrect verification token
        error_message = {"message_text":"Incorrect Verification token"}
        return HttpResponse(json.dumps(error_message), content_type="application/json")

```

{% endmethod %}





