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
```
  "Jwt":{
      "Key" :"YourSecretKeyHere",
      "Issuer" : "YourIssuerHere"
  }
```

### Generate JWT Token:
Create method to generate JWT tokens for authenticated users.
  ```
    public string GenerateToken(string userId)
{
    // Create a new symmetric security key using the secret key from the configuration
    var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_config["Jwt:Key"]));

    // Create signing credentials using the security key and specifying the HMAC SHA-256 algorithm
    var credentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

    // Define the claims to be included in the token
    var claims = new[]
    {
        new Claim(JwtRegisteredClaimNames.Sub, userId),  // Subject claim, typically the user ID
        new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())  // JWT ID claim, unique identifier for the token
    };

    // Create the JWT token with specified parameters
    var token = new JwtSecurityToken(
        issuer: _config["Jwt:Issuer"],  // The issuer of the token, typically your authentication server
        audience: _config["Jwt:Issuer"],  // The audience of the token, typically your API
        claims: claims,  // The claims to be included in the token
        expires: DateTime.Now.AddMinutes(30),  // Set the expiration time for the token
        signingCredentials: credentials  // The signing credentials, which include the key and algorithm
    );

    // Serialize the token to a string format and return it
    return new JwtSecurityTokenHandler().WriteToken(token);
}

  ```

#### Explanation
  1. **Method Definition:**
     ```
       public string GenerateToken(string userId)
     ```
Defines a method GenerateToken that takes a userId as input and returns a JWT token as a string.
Create a Security Key:

csharp
Copy code
var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_config["Jwt:Key"]));
Encoding.UTF8.GetBytes(_config["Jwt:Key"]): Converts the JWT key from the configuration file to a byte array.
new SymmetricSecurityKey(...): Creates a new symmetric security key using this byte array.
Create Signing Credentials:

csharp
Copy code
var credentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);
new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256): Creates signing credentials using the security key and the HMAC SHA-256 algorithm.
Define Claims:

csharp
Copy code
var claims = new[]
{
    new Claim(JwtRegisteredClaimNames.Sub, userId),
    new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
};
new Claim(JwtRegisteredClaimNames.Sub, userId): Creates a claim with the subject (sub) claim type, using the userId provided.
new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()): Creates a claim with the JWT ID (jti) claim type, using a new GUID for uniqueness.
Create the JWT Token:

csharp
Copy code
var token = new JwtSecurityToken(
    issuer: _config["Jwt:Issuer"],
    audience: _config["Jwt:Issuer"],
    claims: claims,
    expires: DateTime.Now.AddMinutes(30),
    signingCredentials: credentials
);
issuer: _config["Jwt:Issuer"]: Sets the issuer of the token from the configuration.
audience: _config["Jwt:Issuer"]: Sets the audience of the token from the configuration.
claims: claims: Adds the claims defined earlier to the token.
expires: DateTime.Now.AddMinutes(30): Sets the token's expiration time to 30 minutes from the current time.
signingCredentials: credentials: Signs the token with the specified credentials.
Serialize and Return the Token:

csharp
Copy code
return new JwtSecurityTokenHandler().WriteToken(token);
new JwtSecurityTokenHandler().WriteToken(token): Serializes the JWT token to a string.
return: Returns the serialized token string.
Summary
This method generates a JWT token using the secret key and issuer specified in your configuration. It includes claims for the subject (user ID) and a unique identifier (JWT ID), signs the token with HMAC SHA-256, and sets an expiration time of 30 minutes. The resulting token is returned as a string.
