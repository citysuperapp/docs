# User Authorization

For user authorization on mini app, we need to integrate both on frontend and backend.

## Frontend Integration

Frontend integration is for mini app to start login process through JSAPI by calling `getSuperAuthCode`:

```js
my.call('getSuperAuthCode', {
  "scopes": 'userinfo,openid' // scope represents level of access an application is requesting
}, function (result) {
  if (result.resultCode == 1000) {
    // Get auth code succesfully
    console.log(`authCode:`, result.authCode);
    this.setData({
      authCode: result.authCode,
    });
    
    // Get access token, merchant server need to implement following API
    my.request({
      url: 'https://merchantserver.com/accessToken',
      method: 'POST',
      data: {
        authCode: result.authCode
      },
      dataType: 'json',
      headers: {
        'content-type': 'application/x-www-form-urlencoded'
      },
      success: function(res) {
        // my.alert({content: JSON.stringify(res)});
        // get token from merchant server here
        // prompt success
        my.showToast({
          content: 'Log in successful',
          type: 'success',
          duration: 2000
        });
      },
      fail: function(res) {
        my.showToast({
          content: 'Log in failed, try again later.',
          type: 'fail',
          duration: 2000
        });
      },
      complete: function(res) {
        // my.alert({title: 'complete'});
      }
    });
  }  else {
    // Show error message
    my.showToast({
      content: 'Log in failed, try again later.',
      type: 'fail',
      duration: 2000
    });
  }
```

## Backend Integration

Once the mini app gets the auth code returned from Open Platform, this auth code will be sent to merchant backend. Merchant backend can then use this code to exchange for access token by requesting OpenAPI: `Get Token (Auth Code)`

Refer to [Open API Doc](https://static.getmya.io/apidoc/SuperOpenAPI.html) for complete requests.