# JWT Authentication demo using .NET Core 2.1 and PostgreSQL database

ASP.NET Core 2.1 - API for User Management, Authentication and Registration

This is a fork of [aspnet-core-registration-login-api](https://github.com/cornflourblue/aspnet-core-registration-login-api), originally created by Jason Watmore.

Please let me know if I have incorrectly credited the original author.

For documentation and instructions check out http://jasonwatmore.com/post/2018/06/26/aspnet-core-21-simple-api-for-authentication-registration-and-user-management

Changes made:
* Modified code to use a PostgreSQL database instead of the inMemory database.
* Add a test project (will contain tests soon).

## Prerequisites
* Download the .NET Core 2.2 SDK
* Download Visual Studio Code
* Install the C# Extension for Visual Studio Code
* Download PostgreSQL

## Steps taken
* Create the Auth.Web directory and put the code from the original repo into it.
* In the main directory, type `dotnet new sln --name <INSERT_NAME_HERE>`. This should initialise a new solution.
* Type `dotnet sln add ./Auth.Web/WebApi.csproj`
* Make a new directory called Auth.Test and navigate to it.
* Type `dotnet new nunit`. This creates an NUnit3 unit test project. NUnit is one of three testing frameworks you can use.

## Adding a new package
* In the solution directory, type `dotnet add ./Experiments/Experiments.csproj package <PACKAGE_NAME>`
* If this throws the error "Unable to load the service index for source https://api.nuget.org/v3/index.json", go into Settings, select Network and Internet > Proxy > Automatic Proxy Setup > and set Automatically detect settings to Off. (source: https://stackoverflow.com/questions/41185443/nuget-connection-attempt-failed-unable-to-load-the-service-index-for-source#41258509). Doing Step 1 should now work.
* For more information, see https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-add-package .

## Integrating with the database (PostgreSQL)
* Create a new database using pgAdmin called `auth_test`. Note the connection details for this database. You will need to fill out the `DataAccessPostgreSqlProvider` field in appsettings.Development.json with these details.
* Download the package `Npgsql.EntityFrameworkCore.PostgreSQL` using the `dotnet add` command.
* Type `dotnet ef migrations add <NAME_OF_MIGRATION>`.
* Check in the migrations folder for the newly created migration. Open the migration file and make any necessary modifications to the schema constraints (if it makes a new table).
* When you are happy with the migration, type `dotnet ef database update`.
* If you need to rollback the database to the previous migration, type `dotnet ef database update <PREVIOUS_MIGRATION_NAME>`. You will have to delete the bad migration manually.
* To reverse all migrations made to the database, type `dotnet ef migrations 0`.

## Running the application
* In the main project directory (not the solution directory), type `dotnet watch run`. It is best to run `dotnet build` beforehand to ensure the project compiles successfully. The console should give you the URL endpoint to interact with the application e.g. `http://localhost:4000`.
* Open Postman and make a POST request to `http://localhost:4000/User/register` with payload body:
```
{
	"firstname": "Test",
	"lastname": "Test",
	"username": "test",
	"password": "password",
	"bio": "This is a test account"
}
```
Ensure you have selected the "raw" radio button and the "JSON (application\json)" option.
* Click "Send". This should register the "test" user with password "password".
* Send a GET request to `http://localhost:4000/User`. You should get a 401 Unauthorised error.
* Login by sending a POST request to `http://localhost:4000/User/authenticate` with payload body:
```
{
	"username": "test",
	"password": "password"
}
```
Ensure you have selected the "raw" radio button and the "JSON (application\json)" option. In the response body, you will receive a JWT token. Make a note of this token string.
*  Send a GET request to `http://localhost:4000/User`, after selecting the Bearer Token option in the Type dropdown of the Authorization pane. Ensure that you have copied the token string from the previous step into the Token field. This should now show a list of all users in the database.
*  In a similar manner to the previous step, send a GET request to `http://localhost:4000/User/1`. If you have included the JWT bearer token, it should show you the details of the user with ID 1.
*  To update a user's details e.g. User 1, send a PUT request to `http://localhost:4000/User/1`.