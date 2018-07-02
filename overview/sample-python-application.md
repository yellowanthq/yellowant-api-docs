# Sample Python Application

YellowAnt makes it insanely easy to convert your internal services into command line applications which you can use within Slack and even create workflows by “piping” your application with other applications. In this post we’ll show you how you can setup a YellowAnt application in minutes. The following is an example of a simple **User** application which will allow your team to query information about the users of a your website that is built with Python\(Django\), through YellowAnt\(@yellowant\) and Slack.

Project Repo — [https://github.com/yellowanthq/sample\_user\_application](https://github.com/yellowanthq/sample_user_application)

## Steps

1. Declare an Application in the YellowAnt Developer console
2. Declare the application’s commands/functions
3. For each application function, declare its arguments/inputs, their datatypes and

   default values

4. Create backend code to authenticate the user\(your team members\), handle the

   command/input data, and respond to the command

## **Step 1: Create an application**

Head over to .yellowant.com/developers/ or click on “Developers” in the top right corner of your Console screen.

![](https://cdn-images-1.medium.com/max/800/1*AoW4awYgGYo-zxBmP2gOZg.png)

![](https://cdn-images-1.medium.com/max/600/1*R6j39KS1gyUdMBWb1iVtcw.png)

Click on “Create new App”. Give a name and invoke name for your application.

We’ll go with “Users” and “users” respectively. Every command that is created for this application will now start with **users**. Click on “Create App” to register the User application.

**Authentication —** Unlike Hubot, Lita, Errbot etc. every YellowAnt user needs to separately authenticate with every application. This creates a unique user-application ‘id’ which helps the application identify the user that is accessing the application command. The authentication is done via OAuth2. By default the following are created when a new application is created:

a\) **client\_id** — The Client ID for the application

b\) **client\_secret** — The client secret for the application. Must be kept secret

c\) **verification\_token** — The verification token that YellowAnt sends with the user command data, so that your server knows that the request is coming from YellowAnt servers.

Click on “Edit app” and set the following:

a\) **redirect\_url** — The url to redirect the user after successful authentication. We can set this to [http://localhost:8000/redirect\_url/](http://localhost:8000/redirect_url/). To authenticate, the user is sent to the authentication url — [https://www.yellowant.com/api/oauth2/authorize/?state=&client\_id=&lt;](https://www.yellowant.com/api/oauth2/authorize/?client_id=YTjgMWGbZD6yNIDHxWcgV59Xp43yaEPufk9jyuL2)your-app-client-id&gt;&response\_type=code&redirect\_url=/. After the user authenticates the application, YellowAnt will send the user to this redirect\_url with GET parameters “code” and “state”. You use the **state** to identify the user and the **code** to exchange for a permanent user token.

b\) **api\_url** — The url to send the command data\(JSON\) as a POST request. We can set this to [http://localhost:8000/apiurl/](http://localhost:8000/redirect/)

We will use all of the above in Step 4 — Server-side implementation.

Also fill out the other information like description, privacy policy url etc. We will also be setting the “Application Visibility” to “Team”, since we want only the team members to be able to use and authenticate with the User application.

## Step 2: Define commands/functions

Let us add the following commands:

1. List users that signed up today with some filter parameters
2. List users that signed up between a date-range
3. Get user details for a particular user with ‘id’ as input
4. Change subscription — takes “id” and “subscription” as inputs

Let’s define command 1, i.e list the users that signed up today with some filters. In the application page, click on “Add Function”.

Give you application an invoke\_name and define the command type as “Command”, request type as “GET” and return type as “JSON” since we will be returning some data with the message, which we will use later. Click on “Save”

## Step 3: Define the function inputs/arguments

Let’s define the arguments for the “list-users-today” command

1. **sort** — -The order to sort the user data\(possible values —**asc** or **dsc**\)

   default ascending\(asc\)

2. **subscription** — The type of subscription \(possible values — **paid** or\*\*

   free\*\*\)

Click on “Add argument” to add the argument and define the name of the argument\(**sort**\), data type\(**varchar**\) and default values\(**asc**\).

Similarly, define an argument for “subscription”. Both these parameters are optional**\(not required\),** and therefore the YellowAnt parser will send the values of these argument only if they are specified in the command.

Follow step 2 and 3 for defining functions and arguments for commands 2 and 3. And you’re done!

## **Step 4: Backend Code**

It’s time to map the commands we defined in the YellowAnt Developer console to the backend code. But first, let’s install the YellowAnt python library to our virtual environment. In your terminal, type the following command

```text
pip install yellowant —-upgrade
```

### 4.1 User Authentication

After you create the User application, every user that wants to use you “User” application must authenticate with YellowAnt with OAuth2.

The following credentials are created when a new application is created on YellowAnt:client\_id, client\_secret, verification\_token

You also provided the redirect\_url and the api\_url for the User application.

Create a button in your website that redirects the user to [https://www.yellowant.com/api/oauth2/authorize/?client\_id=YTjgMWGbZD6yNIDHxWcgV59Xp43yaEPufk9jyuL2](https://www.yellowant.com/api/oauth2/authorize/?client_id=YTjgMWGbZD6yNIDHxWcgV59Xp43yaEPufk9jyuL2)&response\_type=code&redirect\_url=[http://localhost:8000/redirect\_url/](http://localhost:8000/redirect_url/)

Define your redirect\_url view in Django something like below\(More in details in API docs — [here](https://yellowant.com/api/#get-user-profile)\):

```text
def redirect_url(request):
    #User redirected to redirect_url with GET parameter 'code'
    code = request.GET.get("code", False)
    ya_obj = YellowAnt(app_key=settings.YA_CLIENT_ID,
                    app_secret=settings.YA_CLIENT_SECRET,
                    access_token='',
                    redirect_uri=settings.YELLOWANT_REDIRECT_URL)
    #Exchanging 'code' for a permanent access token
    access_token_dict = ya_obj.get_access_token(code)
    access_token = access_token_dict['access_token']
    #Initialize YellowAnt object with access token and get profile
    yellowant_user = YellowAnt(access_token=access_token)
    user_profile = yellowant_user.get_user_profile()
    ya_user_id = user_profile['id']
    #create a user application integration
    user_integration = yellowant_user.create_user_integration()
    integration_id = user_integration['user_application']
    invoke_name = user_integration['user_invoke_name']
    #Store user_application id, along with user token
    return HttpResponse("Authenticated!")
```

### **4.2 Handling user commands**

When a user sends a command YellowAnt parses the command and converts its into a representation of application, function and arguments and sends it to the ‘api\_url’ that we declared when we created the **User** application.

Lets define the view for api\_url

```text
#When a user enters a command, YellowAnt sends the api_url a POST request with the payload that looks something like this:
{
  "data":'{
    "user": 4534,
    "verification_token": "34NaaVTgWhN3QIK74WJX8i1aSoBHMNnvgThygGxi8pPZtJ9OhaIeNlApO8vWkwMV3G0ujUxzrSHZowNoIrxZqa3DnHgi5FJBugjkhws8HaN8U6KkfPo33F1pHiBniYkn",
    "application": 5639,
    "application_invoke_name": "user",
    "function": 74645,
    "function_name": "list-users-today",
    "event": 5043867,
    "args": {
      "sort":"asc",
      "subscription": "paid"
    }
  }'
}
```

## **API URL Code: Catch the command and process**

```text
from yellowant.messageformat import MessageClass

def api_url(request):
    data = json.loads(request.POST.get("data", "{}"))
    command = data["function"]
    service_application = data["application"]
    args = data["args"]
    function_name = data['function_name']
    verification_token = data['verification_token']

    #check verification_token from application page
    if verification_token == "34NaaVTgWhN3QIK74WJX8i1aSoBHMNnvgThygGxi8pPZtJ9OhaIeNlApO8vWkwMV3G0ujUxzrSHZowNoIrxZqa3DnHgi5FJBugjkhws8HaN8U6KkfPo33F1pHiBniYkn":
        try:
            #send the command string to the Command Parser class

          message = AppCommands(command, service_application, args, function_name).parse()
            return HttpResponse(message)
        except CommandException, e:
            message = MessageClass()
            message.message_text = e.message
            return HttpResponse(message.to_json())
    else:
        return HttpResponse(json.dumps({"error": "Incorrect credentials"}), status=403)
```

After the YellowAnt library is installed, go to the Django project and create a file called user\_command\_center.py in our Django application\(within one of your apps\) and define our User application command center. The MessageClass in the yellowant python library takes care of message formatting. More on message formatting [here](https://www.yellowant.com/api/#message-formatting). We will not be dealing with advanced Message formatting like attachments or buttons in this article.

```text
from models import SiteUsers
from yellowant.messageformat import MessageClass, MessageAttachmentsClass, MessageButtonsClass, AttachmentFieldsClass
import datetime
from django.core import serializers



class appCommands(object):
    def __init__(self, function, service_application, args, invoke_name):
        self.function = function
        self.service_application = service_application
        self.args = args
        self.invoke_name = invoke_name

    #Simple message generator
    def generate_simple_message(self, message_text=""):
        message = MessageClass()
        message.message_text = message_text
        return message.to_json()

    #Message parser
    def parse(self):

        #Make sure the following three commands are defined in the developer console with type "Command"

        commands = {
            'list-users-today': self.list_users_today,
            'get-users': self.get_users,
            'change_user_subcription':self.change_user_subcription,
            'get_user_details': self.get_user_details
        }

        #loop through commands, call the appropriate function with args from yellowant
        if str(self.invoke_name) in commands:
            return commands[str(self.invoke_name)](self.args)
        else:
            message = MessageClass()
            message.message_text = "I could not find any command"
            return message.to_json()

    #'list_user_details' function. Takes 'id'(user Id), required
    def list_user_details(self, args):
        if 'id' not in args:
            return self.generate_simple_message("Please provide parameter 'id' in your command")
        else:
            #Check if Id is a number
            if args['id'].is_digit():
                user_id = int(args['id'])
            else:
                return self.generate_simple_message("Please provide an integer value for parameter 'id' in your command")

        try:
            #Searching for user with id
            user_object = SiteUsers.objects.get(id=user_id)
        except SiteUsers.DoesNotExist:
            #return error message if user does not exist
            return self.generate_simple_message("User with that id does not exist")

        user_details_message = MessageClass()
        user_details_message.message_text = "User Id - %d, Fullname - %s, Date joined - %s, Subscription - %s" % (user_id, user_object.full_name, user_object.date_joined, user_object.subscription)
        user_json = serializers.serialize("json", user_object)
        #Adding JSON data to message for use in workflows
        user_details_message.data = user_json
        return user_details_message.to_json()

    #'change_user_subcription' function. Takes 'id'(user Id) and 'subscription' as args, both required
    def change_user_subcription(self, args):
        if 'id' not in args or 'subscription' not in args:
            return self.generate_simple_message("Please provide parameter 'id' and 'subscription' in your command")
        else:
            #Check if Id is a number
            if args['id'].is_digit():
                user_id = int(args['id'])
            else:
                return self.generate_simple_message("Please provide an integer value for parameter 'id' in your command")

        try:
            #Searching for user with id
            user_object = SiteUsers.objects.get(id=user_id)
            #changing subscription value
            user_object.subscription = args['subscription']
        except SiteUsers.DoesNotExist:
            #return error message if user does not exist
            return self.generate_simple_message("User with that id does not exist")


        user_details_message = MessageClass()
        user_details_message.message_text = "User subscription changed"
        user_json = serializers.serialize("json", user_object)
        #Adding JSON data to message for use in workflows
        user_details_message.data = user_json
        return user_details_message.to_json()

    #'list_users_today' function. Takes 'sort' or 'subscription' as args, both optional
    def list_users_today(self, args):
        #Check if 'sort' param is present, else assign default as 'asc'
        if 'sort' in args:
            if args['sort'] in ['asc', 'dsc']:
                order = args['sort']
            else:
                #If invalid, fallback to default value
                order = 'asc'
        else:
            #setting default order 'asc'
            order = 'asc'

        if 'subscription' in args:
            subscription = args['subscription']
        else:
            subscription = None

        #Instantiate a Message Object
        message = MessageClass()
        message.message_text = ""

        #Create list to store the results
        users_list = []

        d_24 = datetime.datetime.now() - datetime.timedelta(days=1)
        if subscription is None:
            users_today = SiteUsers.objects.filter(date_joined__gte=d_24)
        else:
            users_today = SiteUsers.objects.filter(date_joined__gte=d_24, subscription=subscription)

        #Creating User attachments with information of users with a button to fetch user details using the "list_user_details" command
        for user in users_today:
            user_attachment = MessageAttachmentsClass()
            user_attachment.title = user.full_name

            #Showing user data in a "Field"
            date_field = AttachmentFieldsClass()
            date_field.title = "Date Joined"
            date_field.value = user.date_joined
            date_field.short = 1 #Utilize two columns
            user_attachment.attach_field(date_field)

            #Adding a button which calls the "list_user_details" command
            get_user_details_button = MessageButtonsClass()
            get_user_details_button.value = "1" #Give some random value
            get_user_details_button.name = "1" #Give some random value
            get_user_details_button.text =  "Get user details"
            #Encoding command in button
            get_user_details_button.command = {
                "function_name": "list_user_details", "service_application": self.service_application,
                "data": {"id": user.id}
            }
            user_attachment.attach_button(get_user_details_button)

            #Adding a button which provides with a dialog to change the subscription for the user using "change_user_subcription" function
            change_user_sub_button = MessageButtonsClass()
            change_user_sub_button.value = "1" #Give some random value
            change_user_sub_button.name = "1" #Give some random value
            change_user_sub_button.text =  "Change subscription"
            #Encoding command in button
            change_user_sub_button.command = {
                "function_name": "change_user_subcription", "service_application": self.service_application,
                "data": {"id": user.id}, "inputs":['subscription']
            }
            user_attachment.attach_button(change_user_sub_button)

            #Add attachment to message
            message.attach(user_attachment)

        #Adding users data to be used in workflows
        message.data = {"users":serializers.serialize("json", users_today)}
        return message.to_json()
```

## Time to test our new application!

Populate your site users table with some data. Redirect your team member\(or yourself\) to [https://www.yellowant.com/api/oauth2/authorize/?client\_id=YTjgMWGbZD6yNIDHxWcgV59Xp43yaEPufk9jyuL2](https://www.yellowant.com/api/oauth2/authorize/?client_id=YTjgMWGbZD6yNIDHxWcgV59Xp43yaEPufk9jyuL2)&response\_type=code&redirect\_url=[http://localhost:8000/redirect\_url/](http://localhost:8000/redirect_url/)

Authorize the application and then you’ll receive a successful authorization message.

Head over to the YellowAnt console\(or the Slack @yellowant DM\) and try out the following commands:

**1. users list-users-today**

**2. users list-users-today order “dsc”**

**3. users list-users-today subscription “paid”**

And we’re done! Drop us your feedback in your YellowAnt Slack community. You can join [here](https://yellowant-community.herokuapp.com/)!

Here is the project repository — [https://github.com/yellowanthq/sample\_user\_application](https://github.com/yellowanthq/sample_user_application)

* [Python](https://blog.yellowant.com/tagged/python?source=post)
* [Slack](https://blog.yellowant.com/tagged/slack?source=post)
* [Bots](https://blog.yellowant.com/tagged/bots?source=post)
* [Chatbots](https://blog.yellowant.com/tagged/chatbots?source=post)
* [AI](https://blog.yellowant.com/tagged/ai?source=post)

By clapping more or less, you can signal to us which stories really stand out.

## [Vishwa Krishnakumar](https://blog.yellowant.com/@VishwaKk)

Building YellowAnt - [http://www.yellowant.com](http://www.yellowant.com/) — Your intelligent workplace assistant bot

## [YellowAnt](https://blog.yellowant.com/?source=footer_card)

Intelligent workplace assistant bot, connects to many applications and lets you take quick actions and create workflows from within chat platforms like Slack.

