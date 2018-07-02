# Sending messages to the User

YellowAnt allows you to send simple messages to the user in case of new updates or events. There are two ways of sending a message to a user:

1\) **Simple Message** - Use this message to send a normal YellowAnt message to a user about any notification related to the user integration account

2\) **Webhook Message** - This message is exactly like a simple message, except you append **data** along with the message. You will need to define a Webhook function in your YellowAnt application in the developer console and send this message to that webhook function. A user can use a Webhook message to not only notify through YellowAnt, but to also trigger an **Event Workflow.**

