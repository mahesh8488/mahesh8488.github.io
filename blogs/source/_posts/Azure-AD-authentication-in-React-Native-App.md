---
title: Azure AD authentication in React-Native App
date: 2018-02-08 11:06:38
tags:
---

Our requirement was to enable Azure Active Directory authentication for the App. After exploring some options, I found that a library is already available [react-native-azure-ad](https://github.com/wkh237/react-native-azure-ad).


## Installation

Install package from `npm`

```
$ npm install --save react-native-azure-ad
```


## Usage Example

### Login

The following example will show an Azure authorize page in your app, when user successfully logged in, it triggers `onSuccess` method.

```js

import {ReactNativeAD, ADLoginView} from 'react-native-azure-ad'

const CLIENT_ID = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

class LandingView extends React.Component {

  constructor(props) {
    super(props)
    this.AzureADContext = {
      client_id : CLIENT_ID,
      // Optional
      redirectUrl : 'http://localhost:8080',  
      // Optional
      authority_host : 'https://login.microsoftonline.com/common/oauth2/authorize',
      // Optional
      tenant  : 'common',  
      // Optional
      prompt : 'none',
      // This is required if client_id is a web application id
      // but not recommended doing this way.
      client_secret : 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx',
      resources : [
        'https://graph.microsoft.com',
        'https://outlook.office365.com',
        // ... more resources
      ]
    }
  }

  render() {

  new ReactNativeAD({
    client_id: CLIENT_ID,
    resources: [
      'https://outlook.office365.com'
    ]})
  
    return <ADLoginView
              context={ReactNativeAD.getContext(CLIENT_ID)}
              onSuccess={this.onLoginSuccess.bind(this)}/>
  }

  onLoginSuccess(credentials) {
    console.log(credentials[https://outlook.office365.com].access_token)
    // use the access token ..
  }

}

```

### Refresh Token

To make all the API calls use the assureToken method to get token and use it in FETCH calls.

Use `assureToken` method to assure `access_token` of specific resource is valid, when access token is expired, this method will attempt to refresh access token automatically and resolve renewed access token in promise. If it failed to renew the token, the access token in promise will be `undefined`, it means user may have to login again, so you might have to redirect user to ADLoginView for new authorization.


```
ReactNativeAD.getContext(CLIENT_ID).assureToken(RESOURCE_ID).then((token) => {

 // use token ..

})
````


For more details on this: Refer [https://github.com/wkh237/react-native-azure-ad/blob/master/README.md](https://github.com/wkh237/react-native-azure-ad/blob/master/README.md)



