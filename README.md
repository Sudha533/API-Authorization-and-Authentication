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

### Add JWT settings in Appsettings.json
Add a new object for Jwt inside the **'appsettings.json'**
  "Jwt":{
      "Key" :"",
      "Issuer" : ""
  }

