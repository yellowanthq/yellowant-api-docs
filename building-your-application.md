# Building your application

## Receiving user commands

{% method %}

Remember the application, functions and arguments you created above? Now it’s Magic Time!

Whenever a user sends a command on YellowAnt, YellowAnt parses the command string into a command object and sends your API URL the following data as a POST request:



| Parameter |
| :--- |


|  | Description |
| :--- | :--- |
| user | The YellowAnt User Id |
| verification\_token | The verification token assigned to your application during registration. You can use this token to verify if the request is coming from the YellowAnt servers |
| application | Your Application Id |
| application\_invoke\_name | The invoke name the user has assigned to your application |
| function | The Id of the function that the user has called. This is the same as the Id of the function you created in the developer page |
| event\_type | The type of the event. Values can be`command`,`delete`,`webhook_subscription`, or`invoke_name_changed` |
| function\_name | The name of the function the user has called. This is the same as the user\_invoke\_name of the function you created in the developer page |
| event | The event Id associated with the command |
| args | A JSON formatted argument list containing all the arguments parsed in the command that was declared by you in the developer application page |

> SUCCESS You now have the function that the user is trying to call along with the arguments for that function. You are now ready to process that command and send a message to the user.



{% common %}

    Below is an example of a Python - Django Application View that handles an API URL request, processes the command and returns a message


    # Sample POST request data to the API URL of a fictional Github application
    {
      "data":'{
        "user": 4534,
        "verification_token": "bgwreASFth09243rWE134804tnb",
        "application": 5639,
        "application_invoke_name": "github-2",
        "function": 74645,
        "event_type": "command",
        "function_name": "create_issue",
        "event": 5043867,
        "args": {
          "repository":"sithlord/vader",
          "title": "Failed running `bundle exec middleman server`",
          "body": "I get this error when running bundle exec middleman server - /var/lib/gems/2.3.0/gems/middleman-core-4.2.1/lib/middleman-core/extensions.rb:96:in load: Tried to activate old-style extension: deploy. They are no longer supported. (RuntimeError). I am not a ruby developer so cannot find a way around this."
        }
      }'
    }


{% sample lang="py" %}

```py
import json
from ghApp import CommandProcessor

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
        message = CommandProcessor(function_id, service_application, args, function_name).parse()
        # Returning message response
        return HttpResponse(message)
      else:
        # Handling incorrect verification token
        error_message = {"message_text":"Incorrect Verification token"}
        return HttpResponse(json.dumps(error_message), content_type="application/json")

```



{% endmethod %}

