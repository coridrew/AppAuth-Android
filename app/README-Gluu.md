# Using AppAuth with [Gluu](https://gluu.org/docs/)


The Gluu Server is a free open source identity and access management (IAM) platform. The Gluu Server is a container distribution composed of software written by Gluu and incorporated from other open source projects. Configuration of Gluu server is quick and simple. 

If you do not already have a Gluu Server, you can [read the docs](http://gluu.org/docs/ce) to learn how to download and deploy the software for free.
## Create OpenID Client on Gluu server

Install and login to your Gluu server, then follow steps below: 

  1. After login, navigate to https://{{Your_gluu_server_domain}}/identity/client/clientInventory.htm and select **Add Client**
  1. Enter required values to fields. Minimum required fields are :  
	  - Client Name
	  - Client Secret (Although value is required during setup, it is advised not to store in app)
	  - Application Type (Choose **Native** or **Web**)
	  - Pre-Authorization
	  - Persist Client Authorizations
	  - Logout Session Required 
  1. Set **Authentication method for the Token Endpoint:** to **none** to avoid storing client secret in Android app. *To read why to avoid adding a client secret in your app, check official doc by [AppAuth](https://github.com/openid/AppAuth-Android/blob/master/README.md#utilizing-client-secrets-dangerous)* If you still want to use client secret after all of these warnings (NOT ADVISED!), see [README-Gluu-IfYouMustUseClientSecret.md](README-Gluu-IfYouMustUseClientSecret.md).
  1. At the bottom of the page, under **Command:**:
  	1. Click on the **"Add Grant Type"** button. Select **authorization_code** in "Add Grant Type" popup and click Ok. (*Note: **Grant Types:** section, mid-page gets updated.*) 
	1. Click on the **"Add Login Redirect URI"** button. See [this link](https://developers.google.com/identity/protocols/OAuth2InstalledApp#request-parameter-redirect_uri) for information on redirect_uri format. [*ALL Redirect URIs called by app or website MUST exactly match one of the Redirect URI values for the Client pre-registered at the OpenID Provider.*](https://gluu.org/docs/ce/api-guide/openid-connect-api/)  
  1. Click **Add** to redirect to the **Update Client** page, where you can copy the **Client ID** (labeled **"Inum:"**), as it will be needed for the client configuration..
  
  
**Note:** You can also create OpenID Clients by using gluu's oxAuth-rp client, found at https://{{Your_gluu_server_domain}}/oxauth-rp/home.htm 



## Clone the project
Clone https://github.com/openid/AppAuth-Android.git and make the following changes:

Update ``res/raw/auth_config.json`` with your settings. You will get a warning if it is incomplete or invalid. Here is an example JSON configuration: 

```json
{
  "client_id": "{{OpenID Connect Client Inum Value}}",  
  "redirect_uri": "appscheme://client.example.com",  
  "client_secret": "",
  "authorization_scope": "openid email profile",
  "discovery_uri": "https://{{Your_gluu_server_domain}}/.well-known/openid-configuration",
  "authorization_endpoint_uri": "",
  "token_endpoint_uri": "",
  "registration_endpoint_uri": "",
  "https_required": true
}
```

See the main [README.md](README.md) for descriptions of the fields. 

> Setting **client_id** and **client_secret** as blank strings in ``res/raw/auth_config.json`` will automatically register new client to Gluu AppAuth Dynamic Registration end point.  


