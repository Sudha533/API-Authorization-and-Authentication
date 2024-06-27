# Asp.net Web API Authentication and Authorization with JWT
## Steps
  1. Install Packages
  2. Setup Appsettings.json
  3. Update Prgramm.cs

### Install Packages 
  1. Right click **Solutions Explorer** in your project and select Manage **NuGet packages**
  2. Install the following packages
       Microsoft.AspNetCore.Authentication.JwtBearer
       Microsoft.IdentityModel.Tokens
       System.IdentityModel.Tokens.Jwt

### Setup Appsettings.json
Add a new object for Jwt inside the **'appsettings.json'**
  "Jwt":{
      "Key" :"randomstring",
      "Issuer" : "",
      "Audience":"
  }

### Update Program.cs
