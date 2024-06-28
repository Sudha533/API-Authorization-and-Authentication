# Asp.net Web API Authentication and Authorization with JWT
## Steps
  1. Install NuGet Packages Packages
  2. Configure JWT in Program.js
  3. Add JWT Settings in appsettingss.json


### Install NuGet Packages 
  1. Right click **Solutions Explorer** in your project and select Manage **NuGet packages**
  2. Install the following packages
       Microsoft.AspNetCore.Authentication.JwtBearer
       Microsoft.IdentityModel.Tokens
       System.IdentityModel.Tokens.Jwt

### Configure JWT in Program.cs
Add JWT Authentication
  1. Adding Authentication Middleware
     ```
     services.AddAuthentication(options=>{
       options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
       options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
     }
     )
     ```
     
     **Explanation** <br/>
       1. **services.AddAuthentication:** This line starts the configuration of the authentication service in the ASP.NET Core dependency injection container.<br/>
       2. **options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;:** This sets the default scheme to use when a user tries to authenticate.       
       3. **JwtBearerDefaults.AuthenticationScheme** specifies that we are using JWT bearer token authentication. <br/>
       4. **options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;:** This sets the default scheme to use when a challenge occurs, such as when the user tries to access a resource without being authenticated.

  2. Adding JWT Bearer Options :
     ```
     .AddJwtBearer(options =>
     {
       options.TokenValidationParametes = new TokenValidationParameters
         {
           ValidateInssuer = true,
           ValidateAudience = true,
           ValidateLifetime = true,
           ValidateIssuerSigningKey = true,
           ValidIssuer = Configuration["Jwt:Issuer"],
           validAudience = Configuration["Jwt:Issuer"],
           IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(Configuration["Jwt:Key"]))
     };
     });
     ```
     **Explanation**
        1. **.AddJwtBearer:** This method adds the JWT bearer authentication handler with the specified options.
        2. **options.TokenValidationParameters = new TokenValidationParameters:** This line starts defining the parameters used to validate JWT tokens.
             1. **ValidateIssuer = true:** This ensures that the token's issuer is validated. The issuer is the party that issued the JWT token (e.g., your authentication server).
             2. **ValidateAudience = true:** This ensures that the token's audience is validated. The audience is the intended recipient of the token.
             3. **ValidateLifetime = true:** This ensures that the token's lifetime is validated. It checks if the token is expired.
             4. **ValidateIssuerSigningKey = true:** This ensures that the token's signing key is validated. The signing key is used to verify that the token has not been tampered with.
             5. **ValidIssuer = Configuration["Jwt:Issuer"]:** This sets the expected issuer of the token. It retrieves the value from the configuration settings (e.g., appsettings.json).
             6. **ValidAudience = Configuration["Jwt:Issuer"]:** This sets the expected audience of the token. It retrieves the value from the configuration settings. Note that it is common to have the same value for both issuer and audience in many simple setups.
             7. **IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(Configuration["Jwt:Key"])):** This sets the key used to sign the JWT tokens. It retrieves the key from the configuration settings and converts it to a SymmetricSecurityKey.
     

### Add JWT settings in Appsettings.json
Add a new object for Jwt inside the **'appsettings.json'**
  "Jwt":{
      "Key" :"",
      "Issuer" : ""
  }

