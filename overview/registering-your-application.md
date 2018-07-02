# Registering your application

To interact with YellowAnt users, you will first need to create an Application on the YellowAnt developers page.

Go to [https://.yellowant.com/developers/](https://www.yellowant.com/developers/) and Register an application. Your application will need a valid Application name, invoke\_name, API URL\(where we will send all user commands\) and redirect URL\(where we will redirect the user after the user authenticates your application\).

## Defining Application Functions\(Commands\) and their Arguments

After creating your application, you will have to declare the functions which the user will invoke as commands. All functions have a “user\_invoke\_name” which, along with the application invoke name, will point to your function.

You will also need to declare the arguments to your functions so that YellowAnt can parse the necessary arguments and send to your application.

The final command, with the function name and parsed arguments will be sent to your application, along with a verification token, which is automatically created when you register your application.

