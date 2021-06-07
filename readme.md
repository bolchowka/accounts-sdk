# accountsSDK

`accountsSDK` is a small library that installs the "Sign in with LiveChat" button on any website or app. It also wraps OAuth flow in an easy-to-use API.

## Installation
```
npm install --save @livechat/accounts-sdk
```

## Button

Example sign in with LiveChat button designs. Assets are available [here](https://livechat.design/).

![Button](assets/button.svg)
![Small button](assets/button-small.svg)

## Example popup
```
import { AccountsSDK } from '@livechat/accounts-sdk';

// create new SDK instance with it's options
const sdk = new AccountsSDK({
  client_id: '<your_app_client_id>'
});

document.getElementById('login-button').onclick = (e) => {
  if (e && e.preventDefault) {
    e.preventDefault();
  }

  sdk.popup().authorize().then((authorizeData)=>{
    const transaction = sdk.verify(authorizeData);
    if (transaction != null) {
      // authorization success
      // authorizeData contains `accessToken` or `code`
      // transation contains state and optional code_verifier (code + PKCE)
    }
  }).catch((e)=>{
    
  })
};
```

## Flows

Authorize using a popup. It's possible to pass options to override constructor options.
```
const sdk = new AccountsSDK(options)
const promise = sdk.popup(options).authorize()
```

Authorize using iframe. It's possible to pass options to override constructor options. Works when a browser doesn't check for ITP, and user authentication is set.
```
const sdk = new AccountsSDK(options)
const promise = sdk.iframe(options).authorize()
```

Authorize using a full redirect. Authorize function performs full browser redirect to an authorization server. `authorizeData` function checks if authorization is set in URL.
```
const sdk = new AccountsSDK(options)

sdk.redirect().authorizeData().then((authorizeData)=>{
sdk.redirect().authorizeData().then((authorizeData)=>{
  // authorize data found in URL
  const transaction = sdk.verify(authorizeData);

}).catch((e)=>{
  // authorize data missing, redirect to authorization server
  sdk.redirect().authorize()
})
```

## Options

   * `client_id` **string** **required** registered client ID
   * `prompt` **string** use `consent` to force consent prompt in a popup and redirect flows
   * `response_type='token'` **string** OAuth response type, use `token` or `code`
   * `popup_flow='auto'` **string** `auto` - don't show popup when credentials are not required, `manual` - always show popup
   * `state` **string** OAuth state param, auto generated by SDK when empty
   * `verify_state=true` **bool** a function that returns transaction should verify if the state matches
   * `scope` **string** - custom scope list, must be a subset of preconfigured client ID scopes
   * `redirect_uri` **string** OAuth redirect URI - current location by default
   * `email_hint` **string** fill in an email hint in forms
   * `server_url='https://accounts.livechat.com'` **string** authorization server url
   * `path=''` **string** option to provide a path when loading accounts, for example `/signup`
   * `tracking` **object** tracking querystring params
   * `transaction.namespace='com.livechat.accounts'` **string** transaction keys prefix
   * `transaction.key_length=32` **number** transaction random state length
   * `transaction.force_local_storage=false` **bool** try to use local storage instead of cookies
   * `pkce.enabled=true` **bool** Oauth 2.1 PKCE extension enabled for `code` grant
   * `pkce.code_verifier` **string** override auto generated code verifier
   * `pkce.code_verifier_length=128` **number** code verifier length, between 43 and 128 characters https://tools.ietf.org/html/rfc7636#section-4.1
   * `pkce.code_challange_method='S256'` **string** code challange method, use `S256` or `plain`
