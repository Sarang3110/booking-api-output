# booking-api-output
In this Project we are confirm the booking of clients for sport Facilities like Gym or Pool and much more 

AS I HAVE SENND YOU THE SCREENSHOT OF OUTPUTS CHECK IT OUT:-

  Booking Tables Screenshot
➤ Database table storing booking records

Solution Explorer Screenshot
➤ Project structure in Visual Studio

API Sample Request Response Screenshot
➤ JSON request with response preview

Booking Flow Output Screenshot
➤ Console output showing booking steps

Sending Email Screenshot
➤ Email dispatch confirmation via SendGrid




SOME DOCUMENTATION STEPS AS YOU MENTIONED INCLUDED HERE 
************************************************1:- How To Run the Project*************************************************


1:-  As we have Configure The Project on Localhost only.

2:- Opne the Project Solution File in Visual Studio

3:- Now we have to test Booking Api so test the api on Postman or ThunderClient a Swagger is not configured so. (api:- https://pool.localhost:44395/api/bookings  ) 
and pass the data from Body
{
  "FacilityId": "sample string 1",
  "Date": "2025-05-29T20:40:58.0139267+05:30",
  "StartTime": "11:00",
  "EndTime": "13:00",
  "UserName": "Sarang Chavan",
  "UserEmail": "sarangchavan230@gmail.com"
}

As we have used like this by Adding Binding in applicationhost.config file  and by updating the hosts file by adding 127.0.0.1 gym.localhost,
127.0.0.1 localhostb at the end of hosts file.So now we can uae api in this way.


4:- The Api runs By taking the form data and automatically the json file get claaed based on our logic and all the necessary things and booking get successsful.


5:- The Emails Are Already Send When the EnableNoticatifications=true of json file.


6:- We can Publish the App to Staging Enviroment to test the code by doing chnages or adding new features in code. For that we have used Web.Staging.Config






2:- ***************************************** - How tenant config and feature flags work-  ***********************************


Each tenant (gym) and (pool) has its configuration stored in JSON files located in `/App_Data/`. Example:  

JSON Files names are:- tenant-gym.json and tenant-pool.json


Each config file includes:
- `MaxBookingHours`: Max hours allowed per booking
- `FeatureFlags`: JSON object with features enabled or disabled

Example: `tenant-gym.json` and tenant-pool.json
```json
{
  "MaxBookingHours": 2,
  "FeatureFlags": {
    "EnableNotifications": true,
        "EnableAdvancedReports": false

  }
}

 Tenant Resolution
The tenant name is derived from the subdomain using TenantResolver.

The corresponding config file is loaded dynamically.

🚩 Feature Flags
Feature flags are accessed using FeatureFlagManager.

Only enabled features are executed (e.g., SendGrid email is only sent if EnableNotifications is true).



******************************************** 3. DB Setup Instructions****************************************************************************************

a:-  Connection String
Update your Web.config with the correct SQL Server connection:


 <connectionStrings>
  <add name="BookingDbContext" connectionString="Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=GymBookingDB;Integrated Security=True" providerName="System.Data.SqlClient" />
</connectionStrings>

Here in this i have used of own local server database.

b:- DB Schema
The database includes a single table: Bookings.

Booking Table Structure:
Column     	Type
BookingId	UniqueIdentifier
TenantName	NVARCHAR
FacilityId	INT
Date	DateTime
StartTime	Time(7)
EndTime	Time(7)
UserName	NVARCHAR
UserEmail	NVARCHAR
CreatedDate	DateTime

The database is automatically updated using Entity Framework when you run the app.

You have to create the own database in Your SQL SEVER and the cerate your own DBcontext class to do booking operation in Boking modal class to book appointment.
You can create tables manually in database or by creating Migration.(Use Dtaabase First Or Code First Approach).


*********************************************4:- How SendGrid is Integrated Securely**************************************************************************************



API Key Configuration
In Web.config, store your SendGrid API key securely:


<appSettings>
  <add key="SendGridApiKey" value="your-api-key-here" />
</appSettings>
In your code (e.g., EmailService.cs), retrieve it via:


var apiKey = ConfigurationManager.AppSettings["SendGridApiKey"];
Send email only if feature flag is enabled:



if (FeatureFlagManager.IsFeatureEnabled("EnableNotifications"))
{
    EmailService.SendConfirmationEmailAsync(...);
} This ensures:

API key is not hardcoded

Email is only sent when allowed by tenant config based on EnableNotifications flag.



**********************************************************5. Sample API Request and Expected Response**********************************************************************


POST /api/bookings

Sample Request (Input fields from BookingRequest).


{
  "FacilityId": 1,
  "Date": "2025-05-30",
  "StartTime": "2025-05-30T10:00:00",
  "EndTime": "2025-05-30T12:00:00",
  "UserName": "John Doe",
  "UserEmail": "john@example.com"
}


Expected Response
{
  "success": true,
  "message": "Booking successful."
}

Error Response (if exceeding max hours)

{
  "message": "Booking cannot exceed 2 hours."
}




**Notes
1:- Swagger is not configured, but APIs can be tested directly in Postman or Thunder Client.

2: You can add Swagger if interactive documentation is needed

3:- Added New Modal (BookingRequest.cs) beacuse using that we can accept only data from client that required for booking as the client will not send BookingId and
CreatedDate as they are auto genarated so accept clinet sending dat using another modal and then used that in Booking Modal For Booking.

Advantages Using BookingRequest Modal:-


a:- Separation of Concerns
BookingRequest represents only what the client sends.
Booking represents what you store in the database.

b:- Control Over Input
You may want to accept only certain fields from the user and keep others (like BookingId, CreatedDate) internal.

c:- Validation
You can add validation attributes ([Required], [Range], etc.) on the BookingRequest fields for input safety.

d:- Flexibility
Future changes in your database schema won't break your API contracts if you separate input (DTO) and storage (Entity).

4:- Swagger is not configured currently, but can be added if interactive API documentation is required. For now, to run and test the project,
    just open it in Visual Studio and test the endpoints via Postman."





