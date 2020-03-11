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
        .then((UserStatus status) {
          switch (status) {
            case UserStatus.GUEST:
              break;
            case UserStatus.SIGNED_IN:
              // TODO: Handle this case.
              break;
            case UserStatus.SIGNED_OUT:
              // TODO: Handle this case.
              break;
            case UserStatus.SIGNED_OUT_FEDERATED_TOKENS_INVALID:
              // TODO: Handle this case.
              break;
            case UserStatus.SIGNED_OUT_USER_POOLS_TOKENS_INVALID:
              // TODO: Handle this case.
              break;
            case UserStatus.UNKNOWN:
              // TODO: Handle this case.
              break;
            case UserStatus.ERROR:
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
- `FlutterAwsAmplifyCognito.currentUserState()` used for more advanced scenarios, such as determining if the user has Guest credentials, Authenticated with User Pools, has Federated credentials, or has signed out.

```dart
FlutterAwsAmplifyCognito.currentUserState()
    .then((UserStatus userStatus) {
      switch(userStatus) {

        case UserStatus.GUEST:
                      break;
                    case UserStatus.SIGNED_IN:
                      // TODO: Handle this case.
                      break;
                    case UserStatus.SIGNED_OUT:
                      // TODO: Handle this case.
                      break;
                    case UserStatus.SIGNED_OUT_FEDERATED_TOKENS_INVALID:
                      // TODO: Handle this case.
                      break;
                    case UserStatus.SIGNED_OUT_USER_POOLS_TOKENS_INVALID:
                      // TODO: Handle this case.
                      break;
                    case UserStatus.UNKNOWN:
                      // TODO: Handle this case.
                      break;
                    case UserStatus.ERROR:
                      // TODO: Handle this case.
                      break;
      }
    })
    .catchError((error) => print(error));
```
This allows you to write workflows in your application based on the state of the user and what you would like to present on different screens. The `FlutterAwsAmplifyCognito` also offers realtime notifications on user state changes which you can register for in your application using `.addUserStateListener()` as in the code below.

```dart
FlutterAwsAmplifyCognito.addUserStateListener
        .listen((UserStatus userStatus) {
          switch(userStatus) {
            
            case UserStatus.GUEST:
                          break;
                        case UserStatus.SIGNED_IN:
                          // TODO: Handle this case.
                          break;
                        case UserStatus.SIGNED_OUT:
                          // TODO: Handle this case.
                          break;
                        case UserStatus.SIGNED_OUT_FEDERATED_TOKENS_INVALID:
                          // TODO: Handle this case.
                          break;
                        case UserStatus.SIGNED_OUT_USER_POOLS_TOKENS_INVALID:
                          // TODO: Handle this case.
                          break;
                        case UserStatus.UNKNOWN:
                          // TODO: Handle this case.
                          break;
                        case UserStatus.ERROR:
                          // TODO: Handle this case.
                          break;
          }
    })
        .onError((error) {
      print(error);
    });
```

# Working with the API

## Sign Up
Creates a new user in your User Pool:

```dart
final username = _usernameController.text;
final password = _passwordController.text;

Map<String, String> userAttributes = Map<String, String>();
userAttributes['email'] = 'jack@gmail.com';

FlutterAwsAmplifyCognito.signUp(username, password, userAttributes)
    .then((SignUpResult result) {
      if (!result.confirmationState) {
        final UserCodeDeliveryDetails details = result.userCodeDeliveryDetails;
        print(details.destination);
      } else {
        print('Sign Up Done!');
      }
    }).catchError((error) {
        print(error);
    });
```

## Confirm SignUp
Confirms a new user after signing up in a User Pool:

```dart
final username = _usernameController.text;
final code = _codeController.text;

FlutterAwsAmplifyCognito.confirmSignUp(username, code)
    .then((SignUpResult result) {
          if (!result.confirmationState) {
            final UserCodeDeliveryDetails details = result.userCodeDeliveryDetails;
            print(details.destination);
          } else {
            print('Sign Up Done!');
          }
        }).catchError((error) {
            print(error);
        });
```

## Re-send Confirmation Code
```dart
final username = _usernameController.text;

FlutterAwsAmplifyCognito.resendSignUp(username)
    .then((SignUpResult result) {
        print('A verification code has been sent via 
            ${result.userCodeDeliveryDetails.deliveryMedium} 
            at ${result.userCodeDeliveryDetails.destination}')
    }).catchError((error) {
        print(error);
    });
```

## SignIn
Sign in with user credentials:
```dart
FlutterAwsAmplifyCognito.signIn(username, password)
      .then((SignInResult result) {
          switch(result.signInState) {
            case SignInState.SMS_MFA:
              // TODO: Handle this case.
              break;
            case SignInState.PASSWORD_VERIFIER:
              // TODO: Handle this case.
              break;
            case SignInState.CUSTOM_CHALLENGE:
              // TODO: Handle this case.
              break;
            case SignInState.DEVICE_SRP_AUTH:
              // TODO: Handle this case.
              break;
            case SignInState.DEVICE_PASSWORD_VERIFIER:
              // TODO: Handle this case.
              break;
            case SignInState.ADMIN_NO_SRP_AUTH:
              // TODO: Handle this case.
              break;
            case SignInState.NEW_PASSWORD_REQUIRED:
              // TODO: Handle this case.
              break;
            case SignInState.DONE:
              // TODO: Handle this case.
              break;
            case SignInState.UNKNOWN:
              // TODO: Handle this case.
              break;
            case SignInState.ERROR:
              // TODO: Handle this case.
              break;
          }
      }).catchError((error) {
        print(error);
      });
```

## Confirm SignIn
```dart
// Get the confirmation code from the TextField
final confirmSignInChallenge = _codeController.text

FlutterAwsAmplifyCognito.confirmSignIn(confirmSignInChallenge)
    .then((SignInResult result) {
        switch(result.signInState) {
            case SignInState.SMS_MFA:
                // TODO: Handle this case.
                break;
            case SignInState.PASSWORD_VERIFIER:
                // TODO: Handle this case.
                break;
            case SignInState.CUSTOM_CHALLENGE:
                // TODO: Handle this case.
                break;
            case SignInState.DEVICE_SRP_AUTH:
                // TODO: Handle this case.
                break;
            case SignInState.DEVICE_PASSWORD_VERIFIER:
                // TODO: Handle this case.
                break;
            case SignInState.ADMIN_NO_SRP_AUTH:
                // TODO: Handle this case.
                break;
            case SignInState.NEW_PASSWORD_REQUIRED:
                // TODO: Handle this case.
                break;
            case SignInState.DONE:
                // TODO: Handle this case.
                break;
            case SignInState.UNKNOWN:
                // TODO: Handle this case.
                break;
            case SignInState.ERROR:
                // TODO: Handle this case.
                break;
        }
    }).catchError((error) {
        print(error)
    });
```

## Force Change Password
If a user is required to change their password on first login, there is a `NEW_PASSWORD_REQUIRED` state returned when `signIn` is called. You need to provide a new password given by the user in that case. It can be done using `confirmSignIn` with the new password.

```dart
FlutterAwsAmplifyCognito.signIn(username, password)
      .then((SignInResult result) {
          switch(result.signInState) {
            case SignInState.NEW_PASSWORD_REQUIRED:
              print('Please confirm sign-in with new password.');
              break;
            case SignInState.DONE:
              // TODO: Handle this case.
              break;
            default:
              break;
          }
      }).catchError((error) {
        print(error);
}

FlutterAwsAmplifyCognito.confirmSignIn("NEW_PASSWORD_HERE")
    .then((SignInResult result) {
        switch(result.signInState) {
            case SignInState.SMS_MFA:
                print("Please confirm sign-in with SMS.");
                break;
            case SignInState.DONE:
                print("Sign-in done.");
                break;
        }
    }).catchError((error) {
        print(error)
    });
}
```

## Forgot Password
Forgot password is a 2 step process. You need to first call `forgotPassword()` method which would send a confirmation code to user via email or phone number. The details of how the code was sent are included in the response of `forgotPassword()`. Once the code is given by the user, you need to call `confirmForgotPassword()` with the confirmation code to confirm the change of password.

```dart
FlutterAwsAmplifyCognito.forgotPassword(username)
    .then((ForgotPasswordResult result){
      switch(result.state) {
        case ForgotPasswordState.CONFIRMATION_CODE:
          print("Confirmation code is sent to reset password");
          break;
        case ForgotPasswordState.DONE:
          // TODO: Handle this case.
          break;
        case ForgotPasswordState.UNKNOWN:
          // TODO: Handle this case.
          break;
        case ForgotPasswordState.ERROR:
          // TODO: Handle this case.
          break;
      }
    }).catchError((error) {
      print(error);
    });

// username is required for iOS
FlutterAwsAmplifyCognito.confirmForgotPassword(username, newPassword, confirmationCode)
    .then((ForgotPasswordResult result){
        switch(result.state) {
          case ForgotPasswordState.DONE:
           print("Password changed successfully");
            break;
          case ForgotPasswordState.UNKNOWN:
            // TODO: Handle this case.
            break;
          case ForgotPasswordState.ERROR:
            // TODO: Handle this case.
            break;
        }
    }).catchError((error) {
        print(error);
    });
```

## SignOut
```dart
FlutterAwsAmplifyCognito.signOut()
    .then((value) {
        print('Signed Out successfully');
    }).catchError((error) {
      print(error);
    });
```

## Global SignOut
Using global signout, you can signout a user from all active login sessions. By doing this, you are revoking all the OIDC tokens(id token, access token and refresh token) which means the user is signed out from all the devices. However, although the tokens are revoked, the AWS credentials will remain valid until they expire (which by default is 1 hour).
```dart
FlutterAwsAmplifyCognito.signOutGlobally()
    .then((value) {
        print('Signed Out successfully');
    }).catchError((error) {
      print(error);
    });
```

## Utility Properties
The `FlutterAwsAmplifyCognito` provides several property “helpers” that are automatically cached locally for you to use in your application.
```dart
FlutterAwsAmplifyCognito.isSignedIn()
    .then((isSignedIn) {
        print ("Is user signed in?: $isSignedIn");
    }).catchError((error) {
      print(error);
    });

FlutterAwsAmplifyCognito.getUsername()
    .then((username){
        print('username is $username');
    }).catchError((error) {
      print(error);
    });

FlutterAwsAmplifyCognito.getIdentityId()
    .then((identityId){
        print('Identity ID is $identityId');
    }).catchError((error) {
      print(error);
    });

FlutterAwsAmplifyCognito.getUserAttributes()
    .then((userDetails) {
      print('$userAttributes');
    }).catchError((error) {
      print(error);
    });
```

## Managing Security Tokens

When using Authentication with `FlutterAwsAmplifyCognito`, you don’t need to refresh Amazon Cognito tokens manually. The tokens are automatically refreshed by the library when necessary.

## OIDC Tokens
```dart
//All the tokens together
FlutterAwsAmplifyCognito.getTokens()
    .then((Tokens tokens) {
      print('Access Token: ${tokens.accessToken}');
      print('ID Token: ${tokens.idToken}');
      print('Refresh Token: ${tokens.refreshToken}');
    }).catchError((error) {
        print(error);
    });

// Individual tokens
FlutterAwsAmplifyCognito.getIdToken()
      .then((idToken) {
      print('ID Token: $idToken}');
    }).catchError((error) {
      print(error);
    });

    FlutterAwsAmplifyCognito.getAccessToken()
        .then((accessToken) {
      print('Access Token: $accessToken');
    }).catchError((error) {
      print(error);
    });

    FlutterAwsAmplifyCognito.getRefreshToken()
        .then((refreshToken) {
      print('Refresh Token: $refreshToken');
    }).catchError((error) {
      print(error);
    });
```

## AWS Credentials
```dart
FlutterAwsAmplifyCognito.getCredentials()
    .then((AWSCredentials credentials) {
        print('Secret Key: ${credentials.secretKey}');
        print('Access Key: ${credentials.accessKey}');
    }).catchError((error) {
        print(error);
    });
```

# Federated Identities (Social Sign-in)
> Availability Note:<br>
>Currently, the federation feature in the AWSMobileClient supports Cognito Identity Pools only.

Check the instructions for setting Federated Sign In [here](https://aws-amplify.github.io/docs/sdk/android/authentication#federated-identities-social-sign-in)

Available options for sign in are `IdentityProvider.GOOGLE`, `IdentityProvider.AMAZON`, `IdentityProvider.TWITTER`, `IdentityProvider.FACEBOOK`, `IdentityProvider.DEVELOPER`

```dart
FlutterAwsAmplifyCognito.federatedSignIn(IdentityProvider.GOOGLE, "token")
    .then((FederatedSignInResult result) {
      switch(result.userStatus) {
        case UserStatus.GUEST:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_IN:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_OUT:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_OUT_FEDERATED_TOKENS_INVALID:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_OUT_USER_POOLS_TOKENS_INVALID:
          // TODO: Handle this case.
          break;
        case UserStatus.UNKNOWN:
          // TODO: Handle this case.
          break;
        case UserStatus.ERROR:
          // TODO: Handle this case.
          break;
      }
    }).catchError((error) {
      print(error);
    });
```

For `SAML` based authentication with custom ARN you can pass the `customRoleARN` as

```dart
FlutterAwsAmplifyCognito.federatedSignIn(IdentityProvider.GOOGLE, "token", "your_custom_role")
    .then((FederatedSignInResult result) {
      switch(result.userStatus) {
        case UserStatus.GUEST:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_IN:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_OUT:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_OUT_FEDERATED_TOKENS_INVALID:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_OUT_USER_POOLS_TOKENS_INVALID:
          // TODO: Handle this case.
          break;
        case UserStatus.UNKNOWN:
          // TODO: Handle this case.
          break;
        case UserStatus.ERROR:
          // TODO: Handle this case.
          break;
      }
    }).catchError((error) {
      print(error);
    });
```

For developer authenticated identities with Cognito Identity pass the `Cognito Identity ID` as the last parameter
```dart
FlutterAwsAmplifyCognito.federatedSignIn(IdentityProvider.GOOGLE, "token", null, "your_cognito_identity_id")
    .then((FederatedSignInResult result) {
      switch(result.userStatus) {
        case UserStatus.GUEST:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_IN:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_OUT:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_OUT_FEDERATED_TOKENS_INVALID:
          // TODO: Handle this case.
          break;
        case UserStatus.SIGNED_OUT_USER_POOLS_TOKENS_INVALID:
          // TODO: Handle this case.
          break;
        case UserStatus.UNKNOWN:
          // TODO: Handle this case.
          break;
        case UserStatus.ERROR:
          // TODO: Handle this case.
          break;
      }
    }).catchError((error) {
      print(error);
    });
```


# Using Device Features
You can use the device related features of Amazon Cognito UserPools by enabling the `Devices` features. Go to your Cognito UserPool, click on `Devices` in Left Navigation Menu and chose one of `User Opt In` or `Always`.

If you chose `Always` every device used by your application’s users is remembered.

You can read more about the device features in the following [blog](https://aws.amazon.com/blogs/mobile/tracking-and-remembering-devices-using-amazon-cognito-your-user-pools/).

## Terminology
- *Tracked*

When devices are tracked, a set of device credentials consisting of a key and secret key pair is assigned to every device. You can view all tracked devices for a specific user from the Amazon Cognito console device browser, which you can view by choosing a user from the Users panel. In addition, you can see some metadata (whether it is remembered, time it began being tracked, last authenticated time, etc.) associated with the device and its usage.

- *Remembered*

Remembered devices are also tracked. During user authentication, the key and secret pair assigned to a remembered device is used to authenticate the device to verify that it is the same device that the user previously used to sign in to the application. You can also see remembered devices from the Amazon Cognito console.

- *Not Remembered*

A not-remembered device is the flipside of being remembered, though the device is still tracked. The device is treated as if it was never used during the user authentication flow. This means that the device credentials are not used to authenticate the device. The new APIs in the AWS Mobile SDK do not expose these devices, but you can see them in the Amazon Cognito console.

## Remember Device
This option will mark the tracked device as `remembered`
```dart
FlutterAwsAmplifyCognito.trackDevice()
    .then((isTracked){
        print("Tracked successfully: $isTracked");
    }).catchError((error) {
      print(error);
    });
```

## Update Device
This option will mark the tracked device as `not remembered`.
```dart
FlutterAwsAmplifyCognito.untrackDevice()
        .then((isUntracked){
      print("Untracked successfully: $isUntracked");
    }).catchError((error) {
      print(error);
    });
```

## Forget Device
This option will stop tracking the device altogether.

```dart
FlutterAwsAmplifyCognito.forgetDevice()
        .then((isForgotten){
      print("Forgot successfully: $isForgotten");
    }).catchError((error) {
      print(error);
    });
```
Note: Once you call `forget`, you can update the status of the device in the same auth session. The end user will have to sign in again to remember the device.

## Get Device Details
```dart
FlutterAwsAmplifyCognito.getDeviceDetails()
    .then((Device deviceDetails){
        print('createDate: ${deviceDetails. createDate}');
        print('deviceKey: ${deviceDetails.deviceKey}');
        print('lastAuthenticatedDate: ${deviceDetails.lastAuthenticatedDate}');
        print('lastModifiedDate: ${deviceDetails.lastModifiedDate}'); 
        print('attributes: ${deviceDetails.attributes}');
    }).catchError((error) {
        print(error);
    });
```
