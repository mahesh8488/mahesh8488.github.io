---
title: >-
  Resolving Azure Multi-factor authentication error for react-native-azure-ad
  library
date: 2018-08-01 13:38:24
tags: react-native azure-ad authentication Multi-factor-auth
---


react-native-azure-ad is a library which makes azure active directory authentication simpler in React-Native apps. I recently found that if user has Multi-factor authentication enabled

A typical Azure auth will involve user signing in which will return a authorization_code. 
This code is then used to fetch the access_token. However, when user has MFA (Multi-factor authentication) enabled, this call fails throwing the error ***AADSTS50079: The user is required to use multi-factor authentication.***


![Multi-factor auth issue](https://github.com/mahesh8488/static-warehouse/blob/master/images/Multi-factor%20authentication%20error.png?raw=true)



## Attempt to fix #1
Typically, "/token" POST API returns 3 important components: ***id_token, access_token, refresh_token***.

While searching around for solutions, I found that we need to catch this auth error and then fetch token using the "/authorize" endpoint but with query parameter ***response_type=token***.
This works, but the problem is it only returns ***access_token***
To fetch "id_token" I made another call to same api but with ***response_type=id_token".
But, react-native-azure-ad also uses "refresh_token" which is cannot be fetched using any GET APIs. So this solution didn't work for me.


## Solution
The minimal-solution is to catch the authentication error and fetch the token using GET API (/authorize).
Once token is refreshed, the next time you call "/authorize" the authorization_code returned is refreshed.
I have forked the react-native-azure-ad ***[here](https://github.com/mahesh8488/react-native-azure-ad)***.

To use this version in your project, include below in you package.json

````js

"react-native-azure-ad": "https://github.com/mahesh8488/react-native-azure-ad/archive/v1.0.tar.gz",

````



## Explaining my code changes

1. Catch the Auth error
    Look for "interaction_required" in the error message.

````js

    }).catch((err) => {
      this._resourceForWhichAccessTokenIsFetchedByGet = err.resource;
      if (err.response.error.indexOf("interaction_required") > -1) {
        this.setState({visible : true})
        this._lock = false;
        this.props.onVisibilityChange && this.props.onVisibilityChange(false)
        this.setState({page: this._getAcquireTokenPopupUrl(context.getConfig().tenant, err.resource, "token")});
      }

````

````js

_getAcquireTokenPopupUrl(tenant: string, resource: string, response_type: string): string {
    let context = this.props.context
    let redirect = context.getConfig().redirect_uri
    return `https://login.microsoftonline.com/${tenant}/oauth2/authorize?response_type=${response_type}&resource=${resource}` +
      `&client_id=${context.getConfig().client_id}` +
      `&scope=openid&nonce=rnad-${Date.now()}`
  }

````


2. Modify the _handleADToken:

    Look for "access_token", redirect the page to Login. Now that we have fetched the correct access_token, authentication will not break for MFA as the code returned is refreshed.

````js
 _handleADToken(e:{ url:string }):any{
    log.verbose('ADLoginView navigate to', e.url)
    if(this._lock)
      return true
    let code = /((\?|\&)code\=)[^\&]+/.exec(e.url) 

    if(this._needRedirect) {
      // this._needRedirect = false
      return true
    }

    if(this.props.onURLChange) {
      this.props.onURLChange(e)
    }

    if (e.url.indexOf("access_token") > -1) {
      this.setState({visible : true})
      this.props.onVisibilityChange && this.props.onVisibilityChange(false)

      // Once access_token is fetched, go back to login, this time auth will go fine.
      this.setState({page: this._getLoginUrl(this.props.context.getConfig().tenant)});

      return true;
    }
    
    if( code !== null){
      this._lock = true
      log.verbose('ADLoginView._handleADToken code=', code[0])
      code = String(code[0]).replace(/(\?|\&)?code\=/,'')
      this.setState({visible : !this.props.hideAfterLogin})
      this.props.onVisibilityChange && this.props.onVisibilityChange(false)
      this._getResourceAccessToken(code).catch((err) => {
        log.error('ADLoginView._getResourceAccessToken', err)
      })
      return true
    }

    return true

  }
````

Please comment if you found any alternate solutions !
