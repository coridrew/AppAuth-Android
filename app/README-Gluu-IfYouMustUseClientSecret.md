# [Gluu](https://gluu.org/docs/): Using AppAuth with Client Secret (NOT ADVISED!) 

**Note :** According to [this issue](https://github.com/openid/AppAuth-Android/issues/90) using client secret in project is not advised. You must need to a find way keep client secret safe in application. *To read why to avoid adding a client secret in your app, check official doc by [AppAuth](https://github.com/openid/AppAuth-Android/blob/master/README.md#utilizing-client-secrets-dangerous)*
If you still want to pass client secret with token refresh, changes in demo code are needed:

1. **net.openid.appauthdemo.Configuration** :- 
    
    1. create field for client_secret
    ```java
       private String mClientSecret;
    ```
    
    2. In readConfiguration() method add parser for client_secret like this
    ```java
        mClientSecret= getConfigString("client_secret");
    ```   
    
    3. create a getter method for mClientSecret
    ```java
       @Nullable
           public String getClientSecret() {
           return mClientSecret;
       }
    ```


2. **net.openid.appauthdemo.Configuration.TokenActivity**

    1. Change  performTokenRequest with this code.

    ```java
    @MainThread
        private void performTokenRequest(
            TokenRequest request,
            AuthorizationService.TokenResponseCallback callback) {
            ClientAuthentication clientAuthentication;
            if (Configuration.getInstance(TokenActivity.this).getClientSecret() != null) {
                clientAuthentication = new ClientSecretBasic(Configuration.getInstance(TokenActivity.this).getClientSecret());
    
            } else {
                try {
                    clientAuthentication = mStateManager.getCurrent().getClientAuthentication();
                } catch (ClientAuthentication.UnsupportedAuthenticationMethod ex) {
                    Log.d(TAG, "Token request cannot be made, client authentication for the token "
                        + "endpoint could not be constructed (%s)", ex);
                    displayNotAuthorized("Client authentication method is unsupported");
                    return;
                }
            }
            mAuthService.performTokenRequest(
                request,
                clientAuthentication,
                callback);
        }
     ```   



