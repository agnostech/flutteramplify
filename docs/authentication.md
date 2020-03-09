<!-- docs/authentication.md -->

# flutter_aws_amplify_cognito

> Authentication Flutter Plugin for AWS Amplify SDK

- Supports Cognito User Pools and User Identity based login
- Supports Federated Logins (Google, Facebook, Custom, etc.)
- Drop-In UI not supported yet

# Getting started

Follow the instructions mentioned on pub.dev to install the flutter plugin.

## Android

Open terminal in the root folder of your project and navigate to the `android` folder
```shell script
$ cd android
```
Install the **[amplify-cli](https://aws-amplify.github.io/docs/sdk/android/authentication#automated-setup)** and initialize the Authentication automated setup
 ```shell script
$ amplify add auth
```
For customization instructions, check [this](https://aws-amplify.github.io/docs/sdk/android/authentication#automated-setup).

After configuring your Authentication options, update your backend:

```shell script
$ amplify push
```
A configuration file called `awsconfiguration.json` will be copied to your project `./app/src/main/res/raw` directory. The `AWSMobileClient` will leverage this for communicating with backend services. [Click here to learn more about this process.](https://aws-amplify.github.io/docs/sdk/android/start#step-3-how-it-works)

For manual setup, check [this](https://aws-amplify.github.io/docs/sdk/android/authentication#manual-setup)

**Please make sure your final `awsconfiguration.json` file looks similar to this. There must be no code related to OAuth as Drop-In UI is not supported yet.**
```json
{
        "IdentityManager": {
            "Default": {}
        },
        "CredentialsProvider": {
            "CognitoIdentity": {
                "Default": {
                    "PoolId": "XX-XXXX-X:XXXXXXXX-XXXX-1234-abcd-1234567890ab",
                    "Region": "XX-XXXX-X"
                }
            }
        },
        "CognitoUserPool": {
            "Default": {
                "PoolId": "XX-XXXX-X_abcd1234",
                "AppClientId": "XXXXXXXX",
                "AppClientSecret": "XXXXXXXXX",
                "Region": "XX-XXXX-X"
            }
        }
    }
```
Make sure your app's minimum SDK version is 15
```
minSdkVersion 15
```

## iOS

Drag and Drop the same `awsconfiguration.json` file in the `Runner` folder under `ios` directory of your project.

## Usage

Import in your dart code
```dart
import 'package:flutter_aws_amplify_cognito/flutter_aws_amplify_cognito.dart'
```

# Initialization
```dart
FlutterAwsAmplifyCognito.initialize()
        .then((FlutterCognitoUserStatus status) {
          switch (status) {
            case FlutterCognitoUserStatus.GUEST:
              break;
              
            case FlutterCognitoUserStatus.SIGNED_IN:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.SIGNED_OUT:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.SIGNED_OUT_FEDERATED_TOKENS_INVALID:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.SIGNED_OUT_USER_POOLS_TOKENS_INVALID:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.UNKNOWN:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.ERROR:
              // TODO: Handle this case.
              break;
          }
    }).catchError((error) {
      print(error);
    });
```
Since you haven’t logged in yet it will print a state of `SIGNED_OUT`

# State Tracking
`FlutterAwsAmplifyCognito` offers on-demand querying for the “login state” of a user in the application. For instance, you can check if the user is signed-in or not and present an appropriate screen. This is done through a couple of mechanisms:
- `FlutterAwsAmplifyCognito.isSignedIn()` return a BOOLEAN for the most simple use cases
- `FlutterAwsAmplifyCognito.currentUserStatus()` used for more advanced scenarios, such as determining if the user has Guest credentials, Authenticated with User Pools, has Federated credentials, or has signed out.

```dart
FlutterAwsAmplifyCognito.currentUserStatus()
    .then((FlutterCognitoUserStatus userStatus) {
      switch(userStatus) {

        case FlutterCognitoUserStatus.GUEST:
          // TODO: Handle this case.
          break;
        case FlutterCognitoUserStatus.SIGNED_IN:
          // TODO: Handle this case.
          break;
        case FlutterCognitoUserStatus.SIGNED_OUT:
          // TODO: Handle this case.
          break;
        case FlutterCognitoUserStatus.SIGNED_OUT_FEDERATED_TOKENS_INVALID:
          // TODO: Handle this case.
          break;
        case FlutterCognitoUserStatus.SIGNED_OUT_USER_POOLS_TOKENS_INVALID:
          // TODO: Handle this case.
          break;
        case FlutterCognitoUserStatus.UNKNOWN:
          // TODO: Handle this case.
          break;
        case FlutterCognitoUserStatus.ERROR:
          // TODO: Handle this case.
          break;
      }
    })
    .catchError((error) => print(error));
```
This allows you to write workflows in your application based on the state of the user and what you would like to present on different screens. The `FlutterAwsAmplifyCognito` also offers realtime notifications on user state changes which you can register for in your application using `.addUserStateListener()` as in the code below.

```dart
FlutterAwsAmplifyCognito.addUserStateListener
        .listen((FlutterCognitoUserStatus userStatus) {
          switch(userStatus) {
            
            case FlutterCognitoUserStatus.GUEST:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.SIGNED_IN:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.SIGNED_OUT:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.SIGNED_OUT_FEDERATED_TOKENS_INVALID:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.SIGNED_OUT_USER_POOLS_TOKENS_INVALID:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.UNKNOWN:
              // TODO: Handle this case.
              break;
            case FlutterCognitoUserStatus.ERROR:
              // TODO: Handle this case.
              break;
          }
    })
        .onError((error) {
      print(error);
    });
```

# Working with the API

To be continued...
